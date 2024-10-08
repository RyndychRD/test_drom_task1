## Зачем тут ридми?

Здесь будет описаны сделанные мною предположения(так как задача тестовая - не вижу смысла дергать вопросами Татьяну или Алексея. В бою я бы эти предположения отправил на уточнение)
Также я опишу краткие комментарии к используемым подходам с объяснением принципа работы.

### 1. Комментарии к заданию

1. В ТЗ не сказано, что считается числом. Я предполагаю что число - это целое отрицательное или положительное число, а также дробные отрицательные или положительные числа с разделителем ".". Числа по типу 3e2 как 300, 1/2 как 0.5 не будут так считываться
2. В ТЗ не сказано, что является разделителем между числами и другими символами. Я предполагаю что разделитель между числами может быть любой, кроме точки. `3e2` будет распознано как 3 и 2, `1/2` как 1 и 2, `Привет3мир4` как 3 и 4
3. Если встречается запись с несколькими числами, разделенными точками по типу `1.2.3`, то она интерпретируется как 1.2 и все остальное(3). Значит, `1.2.3.4` будет распознано как 1.2 и 3.4
4. Так как речь идет в том числе про числа типа float, то я предполагаю что использоваться будут числа до 3 знака после запятой. Остальное будет округляться по правилам математики

### 2. Комментарии к используемым подходам

#### 1.Рекурсивный

Самый простой в написании, заглядываем в каждую директорию и ищем count. Если вместо файла нашли директорию - запускаем функцию в ней. Если нашли count - увеличиваем общую сумму на суммированное содержимое count. Если больше файлов для рассмотрения не осталось - выходим из текущей итерации

Основная проблема - очень много потребляется памяти и при большой глубине вложенности можно ее исчерпать. Также иногда ставится ограничение на глубину вложенности рекурсии в php.ini, что также повлечет за собой проблемы

#### 2. С помощью массива

Вариант решения задачи без рекурсии. Сначала мы рассматриваем текущую папку. Если встретили директорию - то запоминаем ее в отдельный массив и выкидываем из рассматриваемого массива. Если встретили обычный файл - просто выкидываем. Если встретили count - увеличиваем общую сумму на суммированное содержимое count и выкидываем его из рассматриваемого массива. Когда закончился рассматриваемый массив - добавляем в него запомненные директории из отдельного массива, очищаем отдельный массив и идем дальше по рассматриваемому массиву пока в нем и в отдельном массиве не останется записей

Если бы использовали array_shift, то можно было бы обойтись без дополнительного массива, но он гораздо медленнее чем array_pop. Впрочем, даже с array_pop можно не использовать дополнительный массив, но тогда у нас до самого конца может быть много ненужных записей(обычные файлы из первой директории), которые будут занимать память

Таким образом мы используем минимально необходимое количество памяти и потенциально может обработать сколь угодно большую вложенность. Единственная опасность - в одной директории будет файлов больше, чем мы можем поместить в памяти, но это маловероятный сценарий

#### 3. С помощью системных команд

Так как команда find стандартна для большинства систем Unix, то можно использовать ее для поиска всех count в директории и поддиректориях. После того как нашли - парсим файлы и находим суммы содержимого.

Основная проблема такого подхода - зависимость от конкретной системы и ее настроек. Зато работает очень быстро, так как нет I/O задержки, да и Unix системы с командой find хорошо для таких задач оптимизированы)

Рассматривал только Unix системы, а не Windows, так как подавляющее большинство серверов стоят на Unix системах