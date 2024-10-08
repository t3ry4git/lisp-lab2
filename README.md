<p align="center"><b>МОНУ НТУУ КПІ ім. Ігоря Сікорського ФПМ СПіСКС</b></p>
<p align="center">
<b>Звіт з лабораторної роботи 2</b><br/>
"Рекурсія"<br/>
дисципліни "Вступ до функціонального програмування"
</p>
<p align="right"><b>Студент</b>: Терентьєв Іван Дмитрович КВ-11</p>
<p align="right"><b>Рік</b>: 2024</p>

## Загальне завдання
Реалізуйте дві рекурсивні функції, що виконують деякі дії з вхідним(и) списком(-ами), за можливості/необхідності використовуючи різні види рекурсії. 
Функції, які необхідно реалізувати, задаються варіантом (п. 2.1.1). Вимоги до функцій:
1. Зміна списку згідно із завданням має відбуватись за рахунок конструювання нового списку, а не зміни наявного (вхідного).
2. Не допускається використання функцій вищого порядку чи стандартних функцій для роботи зі списками, що не наведені в четвертому розділі навчального посібника.
3. Реалізована функція не має бути функцією вищого порядку, тобто приймати функції в якості аргументів.
4. Не допускається використання псевдофункцій (деструктивного підходу).
5. Не допускається використання циклів. Кожна реалізована функція має бути протестована для різних тестових наборів. Тести мають бути оформленні у вигляді модульних тестів (див. п. 2.3). Додатковий бал за лабораторну роботу можна отримати в разі виконання всіх наступних умов:
робота виконана до дедлайну (включно з датою дедлайну)
крім основних реалізацій функцій за варіантом, також реалізовано додатковий варіант однієї чи обох функцій, який працюватиме швидше за основну реалізацію, не порушуючи при цьому перші три вимоги до основної реалізації (вимоги 4 і 5 можуть бути порушені), за виключенням того, що в разі необхідності можна також використати стандартну функцію copy-list
## Варіант 7(22)
1. Написати функцію merge-lists-pairs , яка групує відповідні елементи двох
списків:
```lisp
CL-USER> (merge-lists-pairs '(1 2 3 4 5) '(a b c d))
((1 A) (2 B) (3 C) (4 D) (5))
```
2. Написати предикат sublist-after-p , який перевіряє, чи знаходиться після
визначеного елемента списку атомів визначена послідовність елементів (список):
```lisp
CL-USER> (sublist-after-p '(1 a 2 b 3 c 4 d) 'b '(3 c))
T
CL-USER> (sublist-after-p '(1 a 2 b 3 c 4 d) 'b '(3 c d))
NIL
```
## Лістинг функції merge-lists-pairs
```lisp
(defun merge-lists-pairs (list1 list2)
  "Group cars by pairs"
  ;; Go recursive lower every time if not both lists empty
  ;; If both lists empty -> nil
  (if (and (null list1) (null list2))
      nil
    ;; If list1 empty -> return list2 elements as sublists
    (if (null list1)
        (cons (list (car list2)) (merge-lists-pairs nil (cdr list2)))
      ;; If list2 empty -> return list1 elements as sublists
      (if (null list2)
          (cons (list (car list1)) (merge-lists-pairs (cdr list1) nil))
        ;; If both not empty -> return pair 
        (cons (list (car list1) (car list2))
              (merge-lists-pairs (cdr list1) (cdr list2)))))))
```
### Тестові набори
```lisp
(defun run-merge-lists-test (input1 input2 expected-result test-description)
  "Test func for merge-lists-pairs"
  (let ((result (merge-lists-pairs input1 input2)))
    (if (equal result expected-result)
        (format t "~A: successfully.~%" test-description)
        (format t "~A: failed! ~%Expected: ~A~%Got: ~A~%" 
                test-description expected-result result))))

(defun test-merge-lists-pairs ()
  "Testing merge-lists-pairs."
  (format t "Starting testing of merge-lists-pairs...~%")
  (run-merge-lists-test '(1 2 3) '(a b c) '((1 A) (2 B) (3 C)) "Test 1: Lists are having same length")
  (run-merge-lists-test '(1 2 3 4 5) '(a b c d) '((1 A) (2 B) (3 C) (4 D) (5)) "Test 2: First list is longer")
  (run-merge-lists-test '(1 2 3) '(a b c d e) '((1 A) (2 B) (3 C) (D) (E)) "Test 3: Second list is longer")
  (run-merge-lists-test '() '() '() "Test 4: Both lists empty")
  (run-merge-lists-test '(1 2 3) '() '((1) (2) (3)) "Test 5: First list is not NIL, second one is NIL")
  (run-merge-lists-test '() '(a b c) '((A) (B) (C)) "Test 6: Second list is not NIL, first one is NIL")
  (format t "Testing completed.~%"))
```
### Тестування
```lisp
cl-user > (test-merge-lists-pairs)
Starting testing of merge-lists-pairs...
Test 1: Lists are having same length: successfully.
Test 2: First list is longer: successfully.
Test 3: Second list is longer: successfully.
Test 4: Both lists empty: successfully.
Test 5: First list is not NIL, second one is NIL: successfully.
Test 6: Second list is not NIL, first one is NIL: successfully.
Testing completed.
NIL
```
## Лістинг функції sublist-after-p
```lisp
(defun sublist-after-p (main-list target sublist)
  "Checking if sublist exists after target in main-list"
  ;; If main-list is empty -> return NIL
  (if (null main-list)
      nil
    ;; If first element of main-list is a target
    (if (eql (car main-list) target)      
        ;; -> checking that sublists are the same
        (sublist-matches-p (cdr main-list) sublist)
      ;; -> recursively going with cdr main-list
      (sublist-after-p (cdr main-list) target sublist))))

(defun sublist-matches-p (list1 list2)
  "Checking that list1 contains list2"
  ;; If list2 is empty => list1 always contains list2 -> T
  (if (null list2)
      t
    ;; If list1 is empty and list2 is not empty => list1 does not contain list2 -> NIL
    (if (null list1)
        nil
      ;; If first two elements are the same that checking next two elements recursively
      (if (eql (car list1) (car list2))
          (sublist-matches-p (cdr list1) (cdr list2))
        ;; Otherwise -> return NIL
        nil))))
```
### Тестові набори
```lisp
(defun run-sublist-test (main-list target sublist expected-result test-description)
  "Test func for sublist-after-p"
  (let ((result (sublist-after-p main-list target sublist)))
    (if (equal result expected-result)
        (format t "~A: successfully.~%" test-description)
        (format t "~A: failed! ~%Expected: ~A~%Got: ~A~%" 
                test-description expected-result result))))

(defun test-sublist-after-p ()
  "Testing sublist-after-p."
  (format t "Starting testing of sublist-after-p...~%")
  (run-sublist-test '(1 a 2 b 3 c 4 d) 'b '(3 c) t "Test 1: sublist begins after target")
  (run-sublist-test '(1 a 2 b 3 c 4 d) 'b '(3 c d) nil "Test 2: sublist does not begin target")
  (run-sublist-test '(1 2 3 4) 2 '(3 4) t "Test 3: sublist corresponds with last elements")
  (run-sublist-test '(1 2 3 4) 2 '(4 5) nil "Тест 4: sublist is longer than remaining in main-list")
  (run-sublist-test '(1 2 3 4) 5 '(1 2) nil "Тест 5: target is missing in main-list")
  (run-sublist-test '() 'a '(1 2) nil "Тест 6: Empty main-list")
  (run-sublist-test '(1 2 3 4) 2 '() t "Тест 7: Empty sublist")
  (format t "Testing completed.~%"))
```
### Тестування
```lisp
cl-user > (test-sublist-after-p)
Starting testing of sublist-after-p...
Test 1: sublist begins after target: successfully.
Test 2: sublist does not begin target: successfully.
Test 3: sublist corresponds with last elements: successfully.
Тест 4: sublist is longer than remaining in main-list: successfully.
Тест 5: target is missing in main-list: successfully.
Тест 6: Empty main-list: successfully.
Тест 7: Empty sublist: successfully.
Testing completed.
NIL
```


## Додаткове завдання
## Лістинг функції merge-lists-pairs-fast
```lisp
(defun merge-lists-pairs-fast (list1 list2)
  "Group cars by pairs, but faster"
  (let ((result nil))  ;; init result
    (loop while (or list1 list2)  ;; while ((list1 || list2) != NIL) 
          do (let ((pair (cond
                           ((and list1 list2) (list (pop list1) (pop list2)))  ;; if both is not NIL
                           (list1 (list (pop list1)))  ;; if first is not NIL
                           (list2 (list (pop list2)))))) ;; if second is not NIL
               (push pair result)))  ;; add pair
    (nreverse result)))   ;; reverse result
```
### Тестові набори
```lisp
(defun run-merge-lists-fast-test (input1 input2 expected-result test-description)
  "Test func for merge-lists-pairs-fast"
  (let ((result (merge-lists-pairs-fast input1 input2)))
    (if (equal result expected-result)
        (format t "~A: successfully.~%" test-description)
        (format t "~A: failed! ~%Expected: ~A~%Got: ~A~%" 
                test-description expected-result result))))

(defun test-merge-lists-pairs-fast ()
  "Testing merge-lists-pairs-fast."
  (format t "Starting testing of merge-lists-pairs-fast...~%")
  (run-merge-lists-fast-test '(1 2 3) '(a b c) '((1 A) (2 B) (3 C)) "Test 1: Lists are having same length")
  (run-merge-lists-fast-test '(1 2 3 4 5) '(a b c d) '((1 A) (2 B) (3 C) (4 D) (5)) "Test 2: First list is longer")
  (run-merge-lists-fast-test '(1 2 3) '(a b c d e) '((1 A) (2 B) (3 C) (D) (E)) "Test 3: Second list is longer")
  (run-merge-lists-fast-test '() '() '() "Test 4: Both lists empty")
  (run-merge-lists-fast-test '(1 2 3) '() '((1) (2) (3)) "Test 5: First list is not NIL, second one is NIL")
  (run-merge-lists-fast-test '() '(a b c) '((A) (B) (C)) "Test 6: Second list is not NIL, first one is NIL")
  (format t "Testing completed.~%"))
```
### Тестування
```lisp
cl-user > (test-merge-lists-pairs-fast)
Starting testing of merge-lists-pairs-fast...
Test 1: Lists are having same length: successfully.
Test 2: First list is longer: successfully.
Test 3: Second list is longer: successfully.
Test 4: Both lists empty: successfully.
Test 5: First list is not NIL, second one is NIL: successfully.
Test 6: Second list is not NIL, first one is NIL: successfully.
Testing completed.
NIL
```
## Лістинг функції sublist-after-p-fast
```lisp
(defun sublist-after-p-fast (main-list target sublist)
  "Checking if sublist exists after target in main-list"
  ;; Looking for found in main-list
  (let ((found nil))  ;; init found
    (loop for elem in main-list
          until found
          do (if (equal (car main-list) target)
                 (setf found t) ;; if target is found than exit loop
                 (setf main-list (cdr main-list)))) ;; cdring main-list  
    ;; If target is not found -> return NIL
    (if (not found)
        nil
      ;; Otherwise checking that sublist matches
      (loop for elem1 in (cdr main-list)
            for elem2 in sublist
            always (equal elem1 elem2)))))
```
### Тестові набори
```lisp
(defun run-sublist-fast-test (main-list target sublist expected-result test-description)
  "Test func for sublist-after-p-fast"
  (let ((result (sublist-after-p-fast main-list target sublist)))
    (if (equal result expected-result)
        (format t "~A: successfully.~%" test-description)
        (format t "~A: failed! ~%Expected: ~A~%Got: ~A~%" 
                test-description expected-result result))))

(defun test-sublist-after-p-fast ()
  "Testing sublist-after-p-fast."
  (format t "Starting testing of sublist-after-p-fast...~%")
  (run-sublist-fast-test '(1 a 2 b 3 c 4 d) 'b '(3 c) t "Test 1: sublist begins after target")
  (run-sublist-fast-test '(1 a 2 b 3 c 4 d) 'b '(3 c d) nil "Test 2: sublist does not begin target")
  (run-sublist-fast-test '(1 2 3 4) 2 '(3 4) t "Test 3: sublist corresponds with last elements")
  (run-sublist-fast-test '(1 2 3 4) 2 '(4 5) nil "Тест 4: sublist is longer than remaining in main-list")
  (run-sublist-fast-test '(1 2 3 4) 5 '(1 2) nil "Тест 5: target is missing in main-list")
  (run-sublist-fast-test '() 'a '(1 2) nil "Тест 6: Empty main-list")
  (run-sublist-fast-test '(1 2 3 4) 2 '() t "Тест 7: Empty sublist")
  (format t "Testing completed.~%"))
```
### Тестування
```lisp
cl-user > (test-sublist-after-p-fast)
Starting testing of sublist-after-p-fast...
Test 1: sublist begins after target: successfully.
Test 2: sublist does not begin target: successfully.
Test 3: sublist corresponds with last elements: successfully.
Тест 4: sublist is longer than remaining in main-list: successfully.
Тест 5: target is missing in main-list: successfully.
Тест 6: Empty main-list: successfully.
Тест 7: Empty sublist: successfully.
Testing completed.
NIL
```
