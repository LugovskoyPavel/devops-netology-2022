Задача 3. Написание кода.
Цель этого задания закрепить знания о базовом синтаксисе языка. Можно использовать редактор кода на своем компьютере, либо использовать песочницу: https://play.golang.org/.

Напишите программу для перевода метров в футы (1 фут = 0.3048 метр). Можно запросить исходные данные у пользователя, а можно статически задать в коде. Для взаимодействия с пользователем можно использовать функцию Scanf:
```Goland
package main
import "fmt"
func main() {
    fmt.Print("Enter a number: ")
    var input float64
    fmt.Scanf("%f", &input)
    output := input * 2
    fmt.Println(output)    
}
```

# ОТВЕТ:
``` Goland
package ft
import "fmt"
func ft(input float64) {
    fmt.Print("Enter a meters: ")
    fmt.Scanf("%f", &input)
    output := input * 0.3048
    fmt.Println("Value in ft",output)    
}
```

Напишите программу, которая найдет наименьший элемент в любом заданном списке, например:

x := []int{48,96,86,68,57,82,63,70,37,34,83,27,19,97,9,17,}

# ОТВЕТ:
``` Goland
package main
import "fmt"
import "sort"
func main() {
    x := []int{48,96,86,68,57,82,63,70,37,34,83,27,19,97,9,17,}
    sort.Ints(x)
    fmt.Println(x[0])
}
```

Напишите программу, которая выводит числа от 1 до 100, которые делятся на 3. То есть (3, 6, 9, …).

# ОТВЕТ:

package main

import "fmt"

func main() {
  
  for i := 1; i <= 100; i++ {
    
    if i%3 == 0 {
    
    fmt.Println(i)
    
    }
    
    }
  
}

-------------------------------------------------------------------------

В виде решения ссылку на код или сам код.

Задача 4. Протестировать код (не обязательно).
Создайте тесты для функций из предыдущего задания.

Тест для первого примера

сначала немного изменил сам пример

ackage ft

import "fmt"

func Mettoft(input float64) (float64) {
   
   fmt.Print("Enter a meters: ")
    fmt.Scanf("%f", &input)
    output: = input * 0.3048
    fmt.Println("Value in ft",output) 
    return input * 0.3048
}


Потом написал тест 

package ft

import "testing"

func Testft(t *testing.T) {
    
    res := 3.048
     dat := Mettoft(10)
	if dat != res {
        t.Fatal("Error!!!!!")
	}

}





