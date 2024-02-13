Array/List example:

```yaml
Fruits:
- Orange
- Apple
- Banana

Vegetables:
- Carrot
- Cauliflower
- Tomato
```

Dictionary/Map example:

```yaml
Banana:
  Calories: 105
  Fat: 0.4 g
  Carbs: 27 g
  
Grapes:
  Calories: 62
  Fat: 0.3 g
  Carbs: 16 g
```
  
Key-Value/Dictionary/List exmaple:

```yaml
Fruits:
  - Banana:
      Calories: 105
      Fat: 0.4 g
      Carbs: 27 g
  
  - Grapes:
      Calories: 62
      Fat: 0.3 g
      Carbs: 16 g
```
      
Когда использовать массив, а когда словарь? Для хранения свойств одного объекта нужно использовать словарь.

Dictionary in Dictionary example:

```yaml
Color: Blue
Model: 
  Name: Corvette
  Year: 1995
Transmission: Manual
Price: $20,000
```
  
Для хранения списка цветов машин можно использовать массив:

```yaml
- Blue Corvette
- Gray Corvette
- Red Corvette
- Green Corvette
```

Если нужно дополнить каждую машину свойствами, то используем list of dictionaries:

```yaml
- Color: Blue
  Model: 
    Name: Corvette
    Year: 1995
  Transmission: Manual
  Price: $20,000

- Color: Gray
  Model: 
    Name: Corvette
    Year: 1995
  Transmission: Manual
  Price: $22,000

- Color: Red
  Model: 
    Name: Corvette
    Year: 1995
  Transmission: Automatic
  Price: $20,000

- Color: Green
  Model: 
    Name: Corvette
    Year: 1995
  Transmission: Manual
  Price: $23,000
```
  
Dictionary - Unordered, List - Ordered.

Два словаря ниже равны, хоть и значения в разном порядке:

```yaml
Banana:
  Calories: 105
  Fat: 0.4 g
  Carbs: 27 g
  
Banana:
  Calories: 105
  Carbs: 27 g
  Fat: 0.4 g
```

Два списка ниже НЕ равны, т.к. значения в разном порядке:

```yaml
Fruits:
- Orange
- Apple
- Banana

Fruits:
- Orange
- Banana
- Apple
```
