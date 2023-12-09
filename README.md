## Уязвимое приложение

### Инструкция по сборке и запуску приложения
#### Пререквизиты:
```
python
pip
flask
```
#### Пример запуска:
1) Клонируем репозиторий.
1) Переходим в директорию `Attackable`.
2) Запускаем  `python3 main.py`.
3) Переходим по адреесу `http://127.0.0.1:5000`.



### a. XSS
  1. Сначала логинимся под любым пользователем, или создаём своего. В примере я зашел под пользователем `user1:32411`
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/xss1.png)
  2. У каждого пользователя есть возможность установить себе статус. В него мы введём payload
     ```
     <script>alert(1)</script>
     ```
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/xss2.png)
  3. Вывод поддтверждает XSS уязвимость.
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/xss3.png)

### b. IDOR
  IDOR уязвимость заключается в том, что можно использовать горизонтальное и вертикальное повышения привелегий изменив url.
  1. Логинимся под любым пользователем.
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/idor1.png)
  2. Меняем URL с /user/user1 на /user/admin, также можно поменять на /user/user2
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/idor2.png)
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/idor3.png)

### c. SQLI 
  1. Допустим, нам даже не известен логин администратора.
  2. В поле логин вводим payload ```' OR 1=1 -- -```
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/sql1.png)
  4. Используем случайной пароль.
  5. Попытка оказалось успешной. Мы в системе!
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/sql2.png)



### d. OS command injection
1. Переходим на /ping
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/os1.png)
3. Внедрим OS command injection.
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/os2.png)


### e. Path Traversal

1. Откроем любую картинку на главной странице, как отдельную страницу.   
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/path1.png)
2. Заметим как выполняется запрос, после filename= вместо имени картинки, введём путь до /etc/passwd вот таким образом:
```
../../../../../../../etc/passwd
```
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/path2.png)
5. У нас получилось скачать на компьютер `/etc/passwd` с сервера. 
![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/path3.png)

### f. Brute force
Регистрация в этом приложении позволяет использовать пароли состоящие ровно из пяти цифр.
Очевидно, что их можно перебрать, если мы знаем логин.
1. Будем делать это с помощью утилиты kali linux - hydra
2. Для начала нам понадобится список всех возможных паролей. Сгенирируем этот список сами с помощью python:
```
                                                  
import itertools

combinations = itertools.product(range(10), repeat=5)

with open('combinations.txt', 'w') as file:
    for combination in combinations:
        file.write(''.join(map(str, combination)) + '\n')

```
Эта программа запишет все варианты в файл combinations.txt, строка за строкой.
3. Теперь можем запустить перебор.
```
hydra -l admin -P /home/kali/Desktop/combinations.txt -V 127.0.0.1 -s 5000 http-form-post "/login:username=^USER^&password=^PASS^:Invalid credentials, please try again."

```
3. Подбор удался, мы админы!

![alt text](https://github.com/VanyaGorbonos/Attackable/blob/main/photos/brute1.png)
