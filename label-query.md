# Kubernetes Label Query的变迁

## PR#130 Move labels and tests to new package

> Pull Request: https://github.com/kubernetes/kubernetes/pull/130/files


### 第一次重构

此处内容以截止提交**3b980bd**的改动为准。这里只关注pkg/labels/labels.go和pkg/labels/query.go的内容，其余相关的改动请参考PR。  
```
// labels.go

package labels

import (
	"sort"
	"strings"
)

// Labels allows you to present labels independently from their storage.
type Labels interface {
	Get(label string) (value string)
}

// A map of label:value. Implements Labels.
type Set map[string]string

// String方法是为了实现go语言的Stringer接口，用于格式化字符串，以下同理
// All labels listed as a human readable string. Conveiently, exactly the format
// that ParseQuery takes.
func (ls Set) String() string {
	query := make([]string, 0, len(ls))
	for key, value := range ls {
		query = append(query, key+"="+value)
	}
	// Sort for determinism.
	sort.StringSlice(query).Sort()
	return strings.Join(query, ",")
}

// Implement Labels interface.
func (ls Set) Get(label string) string {
	return ls[label]
}
```

这里将Labels定义为一个接口，而非直接定义为map，随便提上两句。  
当前Labels的实现只有Set，也就是内置的map。假设哪天要用自定义的数据结构（比如红黑树）存储label，那么实现Labels接口即可，可以避免一些琐碎的改动。  
这里也许是开放封闭原则？ 私以为开放封闭和依赖倒转两条原则，一定程度上是一而二二而一的东西。

> 开放封闭原则：对扩展开放，对修改封闭。

```
// query.go

package labels

import (
	"fmt"
	"strings"
)

// Represents a label query.
type Query interface {
	// Returns true if this query matches the given set of labels.
	Matches(Labels) bool

	// Prints a human readable version of this label query.
	String() string
}

// 这里返回一个空query，可以匹配所有label
// Everything returns a query that matches all labels.
func Everything() Query {
	return &queryTerm{}
}

// A single term of a label query.
type queryTerm struct {
	// Not inverts the meaning of the items in this term.
	not bool

	// Exactly one of the below three items should be used.

	// If non-nil, we match Set l iff l[*label] == *value.
	label, value *string

	// A list of terms which must all match for this query term to return true.
	and []queryTerm

	// A list of terms, any one of which will cause this query term to return true.
	// Parsing/printing not implemented.
	or []queryTerm
}

// Matches这个方法属实写的很糟，matches变量表达效果太差了
func (l *queryTerm) Matches(ls Labels) bool {
	matches := !l.not
	switch {
	case l.label != nil && l.value != nil:
		if ls.Get(*l.label) == *l.value {
			return matches
		}
		return !matches
	case len(l.and) > 0:
		for i := range l.and {
			if !l.and[i].Matches(ls) {
				return !matches
			}
		}
		return matches
	case len(l.or) > 0:
		for i := range l.or {
			if l.or[i].Matches(ls) {
				return matches
			}
		}
		return !matches
	}

	// Empty queries match everything
	return matches
}

func try(queryPiece, op string) (lhs, rhs string, ok bool) {
	pieces := strings.Split(queryPiece, op)
	if len(pieces) == 2 {
		return pieces[0], pieces[1], true
	}
	return "", "", false
}

// Given a Set, return a Query which will match exactly that Set.
func QueryFromSet(ls Set) Query {
	var query queryTerm
	for l, v := range ls {
		// Make a copy, because we're taking the address below
		label, value := l, v
		query.and = append(query.and, queryTerm{label: &label, value: &value})
	}
	return &query
}

// Takes a string repsenting a label query and returns an object suitable for matching, or an error.
func ParseQuery(query string) (Query, error) {
	parts := strings.Split(query, ",")
	var items []queryTerm
	for _, part := range parts {
		if part == "" {
			continue
		}
		if lhs, rhs, ok := try(part, "!="); ok {
			items = append(items, queryTerm{not: true, label: &lhs, value: &rhs})
		} else if lhs, rhs, ok := try(part, "=="); ok {
			items = append(items, queryTerm{label: &lhs, value: &rhs})
		} else if lhs, rhs, ok := try(part, "="); ok {
			items = append(items, queryTerm{label: &lhs, value: &rhs})
		} else {
			return nil, fmt.Errorf("invalid label query: '%s'; can't understand '%s'", query, part)
		}
	}
	if len(items) == 1 {
		return &items[0], nil
	}
	return &queryTerm{and: items}, nil
}

// Returns this query as a string in a form that ParseQuery can parse.
func (l *queryTerm) String() (out string) {
	if len(l.and) > 0 {
		for _, part := range l.and {
			if out != "" {
				out += ","
			}
			out += part.String()
		}
		return
	} else if l.label != nil && l.value != nil {
		op := "="
		if l.not {
			op = "!="
		}
		return fmt.Sprintf("%v%v%v", *l.label, op, *l.value)
	}
	return ""
}
```

这里的代码属实有一点难懂。  
首先说Query接口，它的Matches方法接收一个Labels作为参数。不管是什么数据结构实现的Labels，Matches具体实现的算法只关心能否Get到键值对。这里体现了设计模式中的依赖倒转原则。
然后说queryTerm结构体，这是Query接口的唯一实现。qeuryTerm的Matches是一个基于多叉树的匹配算法，queryTerm自然是多叉树的节点。根据Matches算法的实现，不难看出queryTerm可以分化出四种节点。  
1) 根据Everything方法可知，没有被初始化的queryTerm是一个空query，匹配所有label。
2) 如果label和value都不为空，queryTerm就可以表达a==b或者a!=b这样的匹配规则，可以把它看作一个叶子节点。
3) 如果and不为空，queryTerm就是一个And节点，当and中所有queryTerm判定为true，Matches的判定结果才为true。
4) 如果or不为空，queryTerm就是一个Or节点，当or中有一个queryTerm判定为true，Matches的判定结果就为true。  

从Matches的实现来看，queryTerm的性质只能是这4种节点中的一种。如果你是个叶子节点，就不能再是And节点或Or节点。如果是And节点就不能再是Or节点。根据ParseQuery的实现来看，当前的版本只支持空节点、叶子节点和And节点。算法很简单，实现很拉垮。

> 依赖倒转原则：程序要依赖于抽象接口，不要依赖于具体实现。

### 第二次重构

此处内容以截止提交**e10e5b9**的改动为准，pkg/labels/labels.go没啥变化，只看pkg/labels/query.go的内容。
```
// query.go

package labels

import (
	"fmt"
	"strings"
)

// Represents a label query.
type Query interface {
	// Returns true if this query matches the given set of labels.
	Matches(Labels) bool

	// Prints a human readable version of this label query.
	String() string
}

// Everything returns a query that matches all labels.
func Everything() Query {
	return andTerm{}
}

type hasTerm struct {
	label, value string
}

func (t *hasTerm) Matches(ls Labels) bool {
	return ls.Get(t.label) == t.value
}

func (t *hasTerm) String() string {
	return fmt.Sprintf("%v=%v", t.label, t.value)
}

type notHasTerm struct {
	label, value string
}

func (t *notHasTerm) Matches(ls Labels) bool {
	return ls.Get(t.label) != t.value
}

func (t *notHasTerm) String() string {
	return fmt.Sprintf("%v!=%v", t.label, t.value)
}

type andTerm []Query

func (t andTerm) Matches(ls Labels) bool {
	for _, q := range t {
		if !q.Matches(ls) {
			return false
		}
	}
	return true
}

func (t andTerm) String() string {
	var terms []string
	for _, q := range t {
		terms = append(terms, q.String())
	}
	return strings.Join(terms, ",")
}

func try(queryPiece, op string) (lhs, rhs string, ok bool) {
	pieces := strings.Split(queryPiece, op)
	if len(pieces) == 2 {
		return pieces[0], pieces[1], true
	}
	return "", "", false
}

// Given a Set, return a Query which will match exactly that Set.
func QueryFromSet(ls Set) Query {
	var items []Query
	for label, value := range ls {
		items = append(items, &hasTerm{label: label, value: value})
	}
	if len(items) == 1 {
		return items[0]
	}
	return andTerm(items)
}

// Takes a string repsenting a label query and returns an object suitable for matching, or an error.
func ParseQuery(query string) (Query, error) {
	parts := strings.Split(query, ",")
	var items []Query
	for _, part := range parts {
		if part == "" {
			continue
		}
		if lhs, rhs, ok := try(part, "!="); ok {
			items = append(items, &notHasTerm{label: lhs, value: rhs})
		} else if lhs, rhs, ok := try(part, "=="); ok {
			items = append(items, &hasTerm{label: lhs, value: rhs})
		} else if lhs, rhs, ok := try(part, "="); ok {
			items = append(items, &hasTerm{label: lhs, value: rhs})
		} else {
			return nil, fmt.Errorf("invalid label query: '%s'; can't understand '%s'", query, part)
		}
	}
	if len(items) == 1 {
		return items[0], nil
	}
	return andTerm(items), nil
}
```

算法还是那个算法，具体实现变了。
原来的queryTerm身兼多重属性，所以它的Matches方法看起来很糊。
这里的hasTerm/notHasTerm/andTerm都实现了Query接口，hasTerm/notHasTerm是叶子节点，andTerm是And条件节点，很好的体现了单一职责原则。
And节点的实现依然具备递归（自包含）的特性，基于多叉树的算法，其复杂度并没有因为设计模式优化而降低。
