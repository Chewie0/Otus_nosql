# Установка
	 docker pull mongodb/mongodb-community-server:latest
 	 docker run --name mongodb -p 27017:27017 -d mongodb/mongodb-community-server:latest
 	


# Создание БД и колекции
 	 mongosh mongodb://127.0.0.1:27017/
 	 use test_Otus_db
 	 db.createCollection('test_collection')

 # Наполнение тестовыми данными
 Данные были импортированы через mongo compass:
 
![image](https://github.com/user-attachments/assets/e6e459c8-f12b-4ec1-a930-bfc3d8ff6e7b)


Добавление через консоль:

![image](https://github.com/user-attachments/assets/3c9bbaa0-17e2-4734-ba07-dfd4f7d1e737)

# Обновление
![image](https://github.com/user-attachments/assets/48b296ad-9640-4cd0-9f5b-9066573ba9d4)


# Выборка
выборка по id

![image](https://github.com/user-attachments/assets/cd53bcf4-b837-483b-912f-bbb2780ba133)

количество документов с pageCount больше 600 

![image](https://github.com/user-attachments/assets/527e6ac2-1958-40f5-af41-301353c15eab)
