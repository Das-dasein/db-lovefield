---
title: 📸 Примеры
nav_order: 5
---

# Лекция 4
![image](https://user-images.githubusercontent.com/33377076/218301027-f1a5e440-dac2-4fd7-abda-16aed59d006c.png)


```javascript
const schemaBuilder = lf.schema.create("university_db", 1);
schemaBuilder
  .createTable("group")
  .addColumn("id", lf.Type.INTEGER)
  .addColumn("department", lf.Type.STRING)
  .addColumn("partTime", lf.Type.BOOLEAN)
  .addPrimaryKey(["id"]);

schemaBuilder
  .createTable("student")
  .addColumn("id", lf.Type.INTEGER)
  .addColumn("fullName", lf.Type.STRING)
  .addColumn("educationStartDate", lf.Type.DATE_TIME)
  .addColumn("groupNumber", lf.Type.INTEGER)
  .addColumn("email", lf.Type.STRING)
  .addColumn("phone", lf.Type.STRING)
  .addPrimaryKey(["id"])
  .addNullable(["phone"])
  .addUnique("uq_email", ["email"])
  .addForeignKey("fk_group", {
    local: "groupNumber",
    ref: "group.id",
  });

schemaBuilder.connect().then((db) => {
  console.log(db.getSchema());
});

```

# Лекция 5

```javascript
const database = await buildSchema();

await saveGroup({
  id: 1,
  department: "информатики",
  partTime: false,
});

const student = {
  id: 1,
  fullName: "Petrov Petr",
  educationStartDate: new Date("2020-09-01"),
  email: "mail@mail.ru",
  phone: "99999999",
  groupNumber: 1,
};

await saveStudent(student);

await updateStudentById({
  id: student.id,
  fullName: student.fullName,
  educationStartDate: new Date("2020-09-01"),
  email: "ya@ya.ru",
  phone: "8880000555035",
  groupNumber: 1,
});

await printAllStudents();

await deleteStudentById(1);

/**
 * Эта функция создаёт таблицы и возвращает Promise с инициализированной б.д.
 */
function buildSchema() {
  const schemaBuilder = lf.schema.create("university_db", 3);

  schemaBuilder
    .createTable("group")
    .addColumn("id", lf.Type.INTEGER)
    .addColumn("department", lf.Type.STRING)
    .addColumn("partTime", lf.Type.BOOLEAN)
    .addPrimaryKey(["id"]);

  schemaBuilder
    .createTable("student")
    .addColumn("id", lf.Type.INTEGER)
    .addColumn("fullName", lf.Type.STRING)
    .addColumn("educationStartDate", lf.Type.DATE_TIME)
    .addColumn("groupNumber", lf.Type.INTEGER)
    .addColumn("email", lf.Type.STRING)
    .addColumn("phone", lf.Type.STRING)
    .addPrimaryKey(["id"])
    .addNullable(["phone"])
    .addUnique("uq_email", ["email"])
    .addForeignKey("fk_group", {
      local: "groupNumber",
      ref: "group.id",
    });

  return schemaBuilder.connect();
}

/**
 * Эта функция сохраняет группу в б.д.
 * @param {object} group - добавляемая группа
 */
function saveGroup(group) {
  const groupTable = database.getSchema().table("group");
  const row = groupTable.createRow(group);

  return database.insertOrReplace().into(groupTable).values([row]).exec();
}

/**
 * Эта функция сохраняет студента в б.д.
 * @param {object} student - добавляемый студент
 */
function saveStudent(student) {
  const studentTable = database.getSchema().table("student");
  const row = studentTable.createRow(student);

  return database.insertOrReplace().into(studentTable).values([row]).exec();
}

/**
 * Эта функция обновляет студента с определённым id
 * @param {object} student - обновляемый студент
 */
function updateStudentById(student) {
  const studentTable = database.getSchema().table("student");

  return database
    .update(studentTable)
    .set(studentTable.fullName, student.fullName)
    .set(studentTable.educationStartDate, student.educationStartDate)
    .set(studentTable.email, student.email)
    .set(studentTable.phone, student.phone)
    .where(studentTable.id.eq(student.id)) // где student.id равен переданному через параметр функции id
    .exec();
}

/**
 * Функция удаляет студента по переданному id
 * @param {string} studentId - id удаляемого студента
 */
function deleteStudentById(studentId) {
  const studentTable = database.getSchema().table("student");

  return database
    .delete()
    .from(studentTable)
    .where(studentTable.id.eq(studentId))
    .exec();
}

/**
 * Обновляет номер телефона определённого студента
 * @param {*} studentId - ид обновляемого студента
 * @param {*} newPhone  - новый номер телефона
 */
function updateStudentPhoneByIdBind(studentId, newPhone) {
  const studentTable = database.getSchema().table("student");

  const query = database
    .update(studentTable)
    .set(studentTable.phone, lf.bind(0))
    .where(studentTable.id.eq(lf.bind(1)));

  return query.bind([newPhone, studentId]).exec();
}

/**
 * Выводит на печать всех студентов
 */
function printAllStudents() {
  const studentTable = database.getSchema().table("student");

  return database.select().from(studentTable).exec().then(console.table);
}

```

# Лекция 6

https://gist.github.com/Das-dasein/88ea85e0f3185222a39f01bb1c884c56#file-lr6-js
