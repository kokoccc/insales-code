*[← InSales Stuff](https://github.com/kokoccc/insales-code)*

# Liquid – вывод элементов массива по N элементов за раз

Проходимся по массиву и получаем на каждой итерации не один элемент, а несколько.

Пример итоговой разметки, в которой каждые 3 изображения товара попали в свой `<div>`:

```html
<section>
  <div>
    <img src="…image1.png">
    <img src="…image2.png">
    <img src="…image3.png">
  </div>
  <div>
    <img src="…image4.png">
    <img src="…image5.png">
    <img src="…image6.png">
  </div>
  <div>
    <img src="…image7.png">
  </div>
</section>
```


## Подготовка

Создаем сниппет с любым названием и содержимым ниже:

```liquid
{%- if arr.size > 0 -%}
  {% assign step = step | default: 1 %}
  {% assign limit = step | at_most: arr.size %}
  {% assign current_arr = arr | slice: 0, step %}

  <div>
  {%- for image in current_arr -%}
    <img src="{{ image.thumb_url }}">
  {%- endfor -%}
  </div>

  {% assign new_arr_length = arr.size | minus: step %}
  {% assign new_arr = arr | slice: step, new_arr_length %}

  {% include 'recursive', arr: new_arr %}
{%- endif -%}
```

Вместо `<div>`, `<img src="{{ item.thumb_url }}">` и `</div>` подставляем свою разметку, остальное не трогаем.


## Использование

Вызываем сниппет в нужном месте в шаблоне и передаем параметры:
  - arr — сам массив
  - step — количество элементов за проход

В нашем примере сниппет называется `images_recursive`:
```liquid
{% include 'images_recursive', arr: product.images, step: 3 %}
```

Старайтесь всегда явно передавать `step`, несмотря на значение по умолчанию 1.  
Иначе может подхватиться тот `step`, что был задан когда-то ранее.


## Как это работает

1. В сниппет передается массив и количество элементов за проход (N)
2. Из первых N элементов создается новый массив `current_arr`
3. Срабатывает цикл по элементам массива `current_arr`
4. Создается новый массив `new_arr`, куда включаются все элементы, кроме первых N
5. Сниппет вызывается рекурсивно и передает самому себе массив `new_arr`
6. Предыдущие шаги повторяются, пока не будет передан пустой массив
