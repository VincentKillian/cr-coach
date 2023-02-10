[Retour](cr-coach.md)

# Mission 3:
Cette mission à pour but de créer la troisième version de l'application où l'on va enregistrer les profils dans une base de donnée local.
Ici SQLite est une base de données idéal pour les données répétitives ou structurées telles que les données utilisateurs.

Afin d'identifier de manière unique les profils, une nouvelle propriétè doit être rajouter dans la classe profil:
C'est la date à laquelle, l'utilisateur controle son taux d'img.
```C#
public class Profil
    {
        private DateTime datemesure;

        private int sexe;
        private int poids;
        private int taille;
        private int age;
        private double img;
        private string message;

        public Profil(DateTime datemesure ,int sexe, int poids, int taille, int age)
        {
            this.datemesure = datemesure;
            this.sexe = sexe;
            this.poids = poids;
            this.taille = taille;
            this.age = age;
            CalculImg();
            ResultatImg();
        }

        public DateTime GetDateMesure() => datemesure;
        ...
    }

```


Ensuite après l'ajoute de cette propriétè "DateTime", il faut implémenter l'accée a la base de donnée SQLite.
Grace à une nouvelle classe ajouter dans le dossier outils:

```C#
using Android.Content;
using Android.Database.Sqlite;
using System;

namespace Coach.outils
{
    class MySQLiteOpenHelper : SQLiteOpenHelper
    {
        private string creation = "CREATE TABLE profil ("
            + "datemesure TEXT PRIMARY KEY,"
            + "poids INTEGER NOT NULL,"
            + "taille INTEGER NOT NULL,"
            + "age INTEGER NOT NULL,"
            + "sexe INTEGER NOT NULL);";

        public MySQLiteOpenHelper(Context contexte, string name, SQLiteDatabase.ICursorFactory factory, int version) : base(contexte, name, factory, version)
        {
        }

        public override void OnCreate(SQLiteDatabase db)
        {
            try
            {
                db.ExecSQL(creation);
            } catch
            {
                throw new NotImplementedException();
            }
        }

        public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
        {
            throw new NotImplementedException();
        }
    }

}
```