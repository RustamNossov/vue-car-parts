# test-task-csi

## Мой первый проект на Vue

##  Краткое описание
Иерархическая таблица

## Технологии
* VueJS 2
* Vue Bootstrap
* fetch API
* json-server
* vue-formulate

## Функционал

* Добавление записи. Все записи сортируются по дате создания. Автоматически пересчитываются суммы. Цена родителя = сумма Стоимостей детей. По согласованию методика расчета отличается от той что в ТЗ.
* Удаление записей. При этом удаляются все дочерние элементы. Суммы пересчитываются. При удалении записи нумерация пересчитывается. 
* Экспорт таблицы в Excel и PDF
* Добавлены спинеры загрузки и сообщения об ошибках 
* Автомотически расчитывается общая сумма

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
