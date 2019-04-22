
package main

import (
	"bufio"
	"fmt"
	"os"
)

// stack
//coded by Zuwy Ling
//vision 1.0
var errorFlag bool = false

type Stack struct {
	Element []interface{} //实现任意类型的Element
}

func NewStack() *Stack {
	return &Stack{}
}

func (stack *Stack) Push(value interface{}) {
	stack.Element = append(stack.Element, value) //压栈操作
}

func (stack *Stack) Top() (value interface{}) {
	if stack.Size() > 0 {
		return stack.Element[stack.Size()-1] //取栈顶元素
	} else {
		return nil
	}
}

func (stack *Stack) IsEmpty() bool {
	return stack.Size() == 0
}

func (stack *Stack) Size() int {
	return len(stack.Element) //取栈的长度
}

func (stack *Stack) Pop() (value interface{}) {
	if stack.Size() > 0 {
		value = stack.Top()
		stack.Element = stack.Element[0 : stack.Size()-1]
		return value //这样直接在出栈时返回一个值了
	} else {
		return nil
	}
}

//打印
func (stack *Stack) Print() {
	for i := len(stack.Element) - 1; i >= 0; i-- {
		fmt.Println(i, "=>", stack.Element[i])
	}
}

//数值和运算符优先级判断
func VerdictValue(value byte) bool {
	if value > 47 && value < 58 { //对应0～9
		return true
	}
	errorFlag = true
	return false
}

func VerdictOperator(value byte) bool {
	switch value {
	case 40, 41, 42, 43, 45, 47: //分别对应()*=-/
		return true
	default:
		errorFlag = true
		return false
	}
}

func OperatorLevel(value byte) int {
	var flag int
	switch value {
	case '(':
		flag = 0
	case ')':
		flag = 0
	case '+':
		flag = 1
	case '-':
		flag = 1
	case '*':
		flag = 2
	case '/':
		flag = 2
	default:
		errorFlag = true
	}
	return flag
}

func topLow(topOp byte, newOp byte) bool { //顶低优先级不出
	if OperatorLevel(topOp) < OperatorLevel(newOp) {
		return true
	} else {
		return false
	}
}

//中缀转后缀
func infix2pofix(infix []byte) string {
	var postfix string = "" //用string方便运算
	var mono byte           //记录临时的中缀表达式中某单值
	var input byte          //记录从interface转换为byte的临时栈顶元素的值
	stack := NewStack()

	for i := 0; i < len(infix); i++ {
		mono = infix[i]
		if VerdictValue(mono) {
			for i < len(infix) && VerdictValue(mono) {
				postfix += string(mono)
				i++
				if i == len(infix) {
					break
				}
				mono = infix[i]
			}
			i-- //判定数值后for循环多走了一位
		} else if VerdictOperator(mono) {
			//分类考虑操作符
			switch mono {
			case '(':
				stack.Push(mono)
			case ')':
				flag := 1
				for !stack.IsEmpty() {
					temp := stack.Top()
					input = temp.(byte)
					if input == '(' {
						stack.Pop()
						flag = 0
						break
					}
					stack.Pop()
					postfix += string(input)
				}
				if stack.IsEmpty() && flag == 1 {
					errorFlag = true //立flag！缺少前括号，报错
				}
			default:
				if !stack.IsEmpty() {
					tempTop := stack.Top()
					top := tempTop.(byte)
					if !topLow(top, mono) {
						postfix += string(top)
						stack.Pop()
						stack.Push(mono)
					} else {
						stack.Push(mono)
					}
				} else {
					stack.Push(mono)
				}
			}
		} else {
			errorFlag = true
		}
	}
	for !stack.IsEmpty() {
		temp := stack.Pop()
		input = temp.(byte)
		postfix += string(input)
	}
	return postfix
}

func postfixCalculate(postfix string) float32 {
	var result float32
	var register [2]float32
	var str []byte = []byte(postfix)
	var value byte

	stack_float32 := NewStack()

	for i := 0; i < len(str); i++ {
		value = str[i]
		if VerdictValue(value) { //转成整数
			var sum float32 = 0
			for i < len(str) && VerdictValue(value) {
				sum = sum*10 + float32(value-48)
				i++
				if i < len(str) {
					break
				}
				value = str[i]
			}
			stack_float32.Push(sum)
			i--
			continue
		} else if VerdictOperator(value) {
			flag := 0
			for ; !stack_float32.IsEmpty() && flag < 2; flag++ { //取两个数进行运算
				temp := stack_float32.Top()
				register[flag] = temp.(float32)
				stack_float32.Pop()
			}
			switch value {
			case 43:
				result = register[1] + register[0]
			case 45:
				result = register[1] - register[0]
			case 42:
				result = register[1] * register[0]
			case 47:
				if register[0] == 0 { // 除数不能为0
					errorFlag = true
					break
				}
				result = register[1] / register[0]
			default:
				break
			}
			stack_float32.Push(result)
		}
	}
	return result
}

func main() {
	scanner := bufio.NewScanner(os.Stdin)
	scanner.Scan()
	raw := scanner.Text()
	var str []byte = []byte(raw)
	postfix := infix2pofix(str)
	fmt.Printf(postfix)
	result := postfixCalculate(postfix)
	fmt.Printf("\n结果为: %.3f", result)
}
