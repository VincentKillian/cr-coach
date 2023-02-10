[Retour](cr-coach.md)

# Mission 2:
Cette mission à pour but de créer la seconde version de l'application qui est d'enregistrer les données, pour les reaffichers après fermeture.

Ici dans cette version, un système de version va être mise en place de serialisation afin de récuperer les données enregistrer en local

On va donc crée une nouvelle classe serializer dans un nouveau dossier outils.
La fonction "Serialize" va créer un fichier via l'objet qu'elle va recevoir.
```C#
using System;
using System.IO;
using System.Runtime.Serialization.Formatters.Binary;

namespace Coach.outils
{
    abstract class Serializer
    {
        public static void serialize(string nomFichier, Object objet)
        {
            string fichier = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData), nomFichier);

            if (File.Exists(fichier))
            {
                File.Delete(fichier);
            }

            FileStream fluxCreate = new FileStream(fichier, FileMode.Create, FileAccess.Write);

            BinaryFormatter bf = new BinaryFormatter();

            try
            {
                bf.Serialize(fluxCreate, objet);
                fluxCreate.Close();
            } 
            catch (Exception ex)
            {
                //Toast.MakeText(this, "Erreur d'enregistrement des données", ToastLength.Long).Show();
            }
        }
    }
}
```

Ensuite la fonction "deserialize" va faire l'inverse elle va recréer un objet via le fichier qu'elle va recevoir.


```C#
public static Object deserialize(string nomFichier)
        {
            Object objet = null;

            string fichier = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData), nomFichier);

            if (File.Exists(fichier))
            {
                FileStream flux = new FileStream(fichier, FileMode.Open);
                
                BinaryFormatter bfr = new BinaryFormatter();

                try
                {
                    objet = bfr.Deserialize(flux);
                    flux.Close();
                }
                catch (Exception ex)
                {
                    //Toast.MakeText(this, "Erreur de récupération des données", ToastLength.Long).Show();
                }
            }
            return objet;
        }
```

Cet outils est donc utilisable partout tant qu'on rajoute a la classe la déclaration de serialization.

Dans notre cas la classe profil doit être serialisable, 
Donc dès que le profil est crée on le sérialize:

```C#
 public void CreerProfil(int unPoids, int uneTaille, int unAge, int unSexe)
        {
            profil = new Profil(unSexe, unPoids, uneTaille, unAge);
            Serializer.serialize(nomFichier, profil);
        }
```

Ensuite on recupere cette serialization dans le controle dès qu'on instancie l'application.

```C#
 public static Controle GetInstance()
        {
            if (Controle.instance == null)
            {
                Controle.instance = new Controle();
                RecupSerialize();

            }
            return Controle.instance;
        }
```

La fonction RecuSérialize les données récupére la desiarilzation du fichier

```C#
private static void RecupSerialize()
        {
            profil = (Profil)Serializer.deserialize(nomFichier);
        }
```

Ensuite on renvoie les données obtenue dans l'application.
```C#
private void RecupProfil()
        {
            if (controle.GetPoids() != 0)
            {
                txtPoids.Text = controle.GetPoids().ToString();
                txtTaille.Text = controle.GetTaille().ToString();
                txtAge.Text = controle.GetAge().ToString();

                rdFemme.Checked = true;
                if (controle.GetSexe() == 1)
                {
                    rdHomme.Checked = true;
                }

                double img = this.controle.GetImg();
                string message = this.controle.GetMessage();

                if (message == "Parfait")
                {
                    imgSmiley.SetImageResource(Resource.Drawable.Smiley_Ok);
                    lbIMG.SetTextColor(Android.Graphics.Color.DarkGreen);
                } else if (message == "Trop maigre")
                {
                    imgSmiley.SetImageResource(Resource.Drawable.Smiley_PasTop);
                    lbIMG.SetTextColor(Android.Graphics.Color.DarkOrange);
                } else
                {
                    imgSmiley.SetImageResource(Resource.Drawable.Smiley_No);
                    lbIMG.SetTextColor(Android.Graphics.Color.DarkRed);
                }
                lbIMG.Text = String.Format("{0:0.00}", img) + "% " + message;
            }
        }
```

Apres avoir fermer l'application les données seront enregister dans un fichier, en attendant la réouverture de celle-ci pour les réintegrer dans l'application.