# Création E-Commerce Django Vue

# Back-end - Install Django and setup
> python3 --version

> virtualenv env
> source env/bin/activate

Donc après cela, pip ne va installer plus que dans cet environnement!
> pip install django django-rest-framework django-cors-headers djoser pillow stripe

- djoser pour tout ce qui est login, auth
- pillow pour les images

Pour créer un projet : 
> django-admin startproject my_projekt

Une fois dans le dossier/, on a manage.py pour gérer connexion à la BDD, tâches administratives etc...

Ensuite : 
- asgi.py et wsgi.py sont les points d'entrée pour le serveur web
- settings.py pour la config global du projet

Dans ce settings py:
- ajouter dans INSTALLED_APPS justement nos packages du projet:
    'rest_framework',
    'rest_framework.authtoken',
    'corsheaders',
    'djoser',

- dans MIDDLEWARE ajouter:
    'corsheaders.middleware.CorsMiddleware',

## Pattern 

Une fois le modèle créé:
Cheminement : models -> serializers -> views -> urls

- models.py : On décrit le modèle sous forme de Class et d'attributs pour représenter la table et les champs SQL (types)
- serializers.py : ??? sert à formater les data ?
- views.py : C'est là qu'on va créer nos objets views, en les récupérant de la BDD par exemple avec .all()
- urls.py : On décrit la route sur laquelle on va taper. Puis appel des views ex : "views.LatestProductsList.as_view()),"
- admin.py : Sert à register() des Entités dans le Back-Office


### Models

Pour créer un model :
> python3 manage.py startapp product

Dedans on va avoir plusieurs fichiers, aller d'abord dans models.py:

```
class Category(models.Model):
    name = models.CharField(max_length=255)
    slug = models.SlugField()

    class Meta: 
        ordering = ('name',)

    # Pour éviter de renvoyer l'adresse de l'objet
    def __str__(self):
        return self.name

    def get_absolute_url(self):
        return f'/{self.slug}'

class Product(models.Model):
    category = models.ForeignKey(Category, related_name='products', on_delete=models.CASCADE)
    name = models.CharField(max_length=255)
    slug = models.SlugField()
    description = models.TextField(blank=True, null=True)
    price = models.DecimalField(max_digits=6, decimal_places=2)
    image = models.ImageField(upload_to='uploads/', blank=True, null=True)
    thumbnail = models.ImageField(upload_to='uploads/', blank=True, null=True)
    date_added = models.DateTimeField(auto_now_add=True)

    class Meta: 
        ordering = ('date_added',)

    # Pour éviter de renvoyer l'adresse de l'objet
    def __str__(self):
        return self.name

    def get_absolute_url(self):
        return f'/{self.category.slug}/{self.slug}/'

    def get_image(self):
        if self.image:
            return 'http://127.0.0.1:8000' + self.image.url
        return ''

    
    def get_thumbnail(self):
        if self.image:
            return 'http://127.0.0.1:8000' + self.thumbnail.url
        else: 
            if self.image:
                self.thumbnail = self.make_thumbnail(self.image)
                self.save()
                return 'http://127.0.0.1:8000' + self.thumbnail.url
            else:
                return ''
    
    def make_thumbnail(self, image, size=(300,200)):
        img = Image.open(image)
        img.convert('RGB')
        img.thumbnail(size)

        thumb_io = BytesIO()
        img.save(thumb_io, 'JPEG', quality=85) 

        thumbnail = File(thumb_io, name=image.name)

        return thumbnail
```

On doit aussi informer settings.py de notre produit, dans INSTALLED_APPS ajouter 'product'.

Il faut dans settings ajouter pour gérer les urls d'uploads:
```
STATIC_URL = '/static/'
MEDIA_URL = '/media/'

MEDIA_ROOT = BASE_DIR / 'media/'
```
Et dans urls.py (TBC -> Expliquer why)
```
urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/v1/',  include('djoser.urls')),
    path('api/v1/',  include('djoser.urls.authtoken')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### BDD & Migrations
> python3 manage.py makemigrations
> python3 manage.py migrate

> python3 manage.py createsuperuser
> python3 manage.py runserver



# VueJS
> npm i -g @vue/cli && vue create kommerz_vue
> npm run serve

On va faire du axios - bulma - scss avec dart-preprocesseur
Dans app.vue dans 
<style> @import '../node_modules/bulma'; </style>

Quelques règles à connaître : 

Toggle au click:
    - @click="showMobileMenu = !showMobileMenu

Vue directive (si showMobileMenu est true alors la class is-active): 
    - v-bind:class="{'is-active': showMobileMenu}"

Creuser methods vs computed.

String vs variable props :
```
initialItem="item" VS :initialItem="item" VS :initialItem="'item'"
```

@ est l'équivalent de v-on
### Exemple d'un script component

```
<script>
import axios from 'axios';

export default {
  name: 'Home',
  data() {
    return {
      latestProducts:[]
    }
  },
  component: {

  },
  mounted() {
    this.getLatestProducts()
  },
  methods: {
    getLatestProducts() {
      axios.get('/api/v1/latest-products/')
      .then(res => {
        this.latestProducts = res.data;
      })
      .catch(error => {
        console.log(error)
      })
    }
  }
}
</script>
```