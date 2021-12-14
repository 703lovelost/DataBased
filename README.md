# Лабораторная №8 - Работа с базой данных SQLite

В лабораторной реализована возможность работы с базой данных: внесение, обновление, удаление, чтение и обновление

Класс внедрения базы данных:

```java
public class DBHelper extends SQLiteOpenHelper {
    public static final int DATABASE_VERSION = 1;
    public static final String DATABASE_NAME = "myBase";
    public static final String TABLE_PERSONS = "persons";
    public static final String KEY_ID = "_id";
    public static final String KEY_NAME = "name";
    public static final String KEY_MAIL = "mail";

    public DBHelper(@Nullable Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL("create table " + TABLE_PERSONS + "(" + KEY_ID + " integer primary key," + KEY_NAME + " text," + KEY_MAIL + " text" + ")");
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("drop table if exists " + TABLE_PERSONS);
        onCreate(db);
    }
}
```

Метод работы кнопок:

```java
    @Override
    public void onClick(View v)
    {
        String ID = etID.getText().toString();
        String name = etName.getText().toString();
        String email = etEmail.getText().toString();

        SQLiteDatabase database = dbHelper.getWritableDatabase();
        ContentValues contentValues = new ContentValues();

        switch (v.getId())
        {
            case R.id.buttonAdd:
                contentValues.put(DBHelper.KEY_NAME, name);
                contentValues.put(DBHelper.KEY_MAIL, email);
                database.insert(DBHelper.TABLE_PERSONS, null, contentValues);
                break;

            case R.id.buttonRead:
                Cursor cursor = database.query(DBHelper.TABLE_PERSONS, null, null, null,
                        null, null, null);

                if (cursor.moveToFirst())
                {
                    int idIndex = cursor.getColumnIndex(DBHelper.KEY_ID);
                    int nameIndex = cursor.getColumnIndex(DBHelper.KEY_NAME);
                    int emailIndex = cursor.getColumnIndex(DBHelper.KEY_MAIL);
                    do {
                        Log.d("mLog", "ID =" + cursor.getInt(idIndex) +
                                ", name = " + cursor.getString(nameIndex) +
                                ", email = " + cursor.getString(emailIndex));

                    } while (cursor.moveToNext());
                } else
                    Log.d("mLog", "0 rows");

                cursor.close();
                break;

            case R.id.buttonClear:
                database.delete(DBHelper.TABLE_PERSONS, null, null);
                break;

            case R.id.buttonDelete:
                if (ID.equalsIgnoreCase(""))
                {
                    break;
                }
                int delCount = database.delete(DBHelper.TABLE_PERSONS, DBHelper.KEY_ID + "= " + ID, null);
                Log.d("mLog", "Удалено строк = " + delCount);

            case R.id.buttonUpdate:
                if (ID.equalsIgnoreCase(""))
                {
                    break;
                }
                contentValues.put(DBHelper.KEY_MAIL, email);
                contentValues.put(DBHelper.KEY_NAME, name);
                int updCount = database.update(DBHelper.TABLE_PERSONS, contentValues, DBHelper.KEY_ID + "= ?", new String[] {ID});
                Log.d("mLog", "Обновлено строк = " + updCount);
        }
        dbHelper.close();
    }
```
    

Apk-файл добавлен в репозиторий.
