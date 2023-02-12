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
