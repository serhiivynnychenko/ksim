# Комп'ютерні системи імітаційного моделювання
## Лабораторна робота №**2**. Редагування імітаційних моделей у середовищі NetLogo
СПм-24-2, Винниченко Сергій Андрійович
<br>
Варіант 5, [Fire Simple Extension 2](http://www.netlogoweb.org/launch#http://www.netlogoweb.org/assets/modelslib/IABM%20Textbook/chapter%203/Fire%20Extensions/Fire%20Simple%20Extension%202.nlogo). 

<br>

### Внесені зміни у вихідну логіку моделі, за варіантом:

**Додано агентів-"пожежників", кількість яких вказується користувачем.**
<pre>
create-turtles num-firefighters [
  set shape "person"
  set size 10
  set color blue
  move-to one-of patches with [pcolor = black]
]
</pre>
**Агенти початково розміщені тільки на "пустих" клітинках (де нема дерев) випадковим чином...**
<pre>
move-to one-of patches with [pcolor = black]
</pre>
**...і можуть переміщатися у випадковому напрямку, але тільки по тим плямам, що поки що не згоріли (мають зелений чи чорний колір)**
<pre>
  ask turtles [
    let target one-of patches with [pcolor = red]
    if target != nobody [
      face target
      
      let next-patch patch-ahead 1
      
      if next-patch != nobody and next-patch != prev-patch and ([pcolor] of next-patch = green or [pcolor] of next-patch = black or [pcolor] of next-patch = red) [
        forward 1
      ] 
      if next-patch = nobody or next-patch = prev-patch or not ([pcolor] of next-patch = green or [pcolor] of next-patch = black or [pcolor] of next-patch = red) [
        let safe-patches patches in-radius 1 with [pcolor = green or pcolor = black and self != [prev-patch] of myself]
        if any? safe-patches [
          move-to one-of safe-patches
        ]
        if not any? safe-patches [
          die
        ] 
      ]
      
      set prev-patch patch-here
      
      let fire-neighbors patches in-radius 1 with [pcolor = red]
      
      ask fire-neighbors [
        set pcolor green
      ]
    ]
  ]
</pre>
**При зустрічі пожежниками джерел розповсюдження вогню (червоних клітинок) "гасити" їх.**
<pre>
  let fire-neighbors patches in-radius 1 with [pcolor = red]
      
  ask fire-neighbors [
    set pcolor green
  ]
</pre>

### Внесені зміни у вихідну логіку моделі, на власний розсуд:

**Тепер пожежа виникає у рандомному місці та має радіус 10**.
<pre>
ask one-of patches with [pcolor = green] [
  set pcolor red;
  let green-patches patches in-radius 10 with [pcolor = green];
  
  ask green-patches [
    set pcolor red;
  ]
]
</pre>

замість

<pre>
  if pxcor = min-pxcor
    [ set pcolor red ]
</pre>

**Тепер вогонь може поширюватись також по діогоналі**

<pre>
  let green-patches patches in-radius 1 with [pcolor = green];
    
  ask green-patches [
  ...
</pre>

замість

<pre>
  ask neighbors4 with [ pcolor = green ] [
  ...
</pre>

**Пожежники намагаються бігти до вогню, а не просто переміщаются випадково**

<pre>
  face target
  
  let next-patch patch-ahead 1
      
  if next-patch != nobody and next-patch != prev-patch and ([pcolor] of next-patch = green or [pcolor] of next-patch = black or [pcolor] of next-patch = red) [
    forward 1
  ]
</pre>

**Додано екран з лічильником пожежників**

<img width="768" height="542" alt="image" src="https://github.com/user-attachments/assets/bd1054b2-8b34-4e51-b25d-55eff43fb80b" />


Фінальний код моделі та її інтерфейс доступні за [посиланням](https://github.com/serhiivynnychenko/ksim/blob/main/lab2/model.nlogox). 
<br>

## Обчислювальні експерименти
### Вплив кількості пожежників на відсоток згорілих дерев
*density* - 79%<br>
*probability-of-spread* - 60%<br>
*south-wind-speed* - -20<br>
*west-wind-speed* - 15<br>
| Кількість пожежників (num-firefighters) | Згоріло min% (5 спроб) | Згоріло max% (5 спроб) |
|-----------------------------------------|------------------------|------------------------|
| 0 | 5.3 | 14 |
| 50 | 1 | 13 |
| 100 | 1.8 | 4.1 |
| 150 | 0.9 | 2.5 |
| 200 | 0.9 | 1.9 |

<img width="987" height="421" alt="image" src="https://github.com/user-attachments/assets/d763df8b-31fe-4bf6-8284-054de1f20221" />
Навіть невелика кількість пожежників значно зменшує площу згорілих дерев, а збільшення їх числа до 100–200 майже повністю запобігає поширенню вогню; при цьому важлива початкова відстань розміщення пожежників від осередку загоряння — чим ближче, тим ефективніше гасіння.
