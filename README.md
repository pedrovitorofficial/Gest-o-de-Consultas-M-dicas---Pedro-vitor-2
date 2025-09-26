# Gest-o-de-Consultas-M-dicas---Pedro-vitor-2
myproject/
├── myproject/
│   ├── settings.py
│   └── urls.py
├── usuarios/
│   ├── templates/
│   │   └── usuarios/
│   │       ├── base.html
│   │       ├── login.html
│   │       ├── cadastro.html
│   │       └── home.html
│   ├── views.py
│   └── urls.py
└── manage.py

myproject/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]

myproject/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('usuarios.urls')),
]

usuarios/views.py
from django.shortcuts import render, redirect
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth import login

def cadastro_view(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            user = form.save()
            login(request, user)
            return redirect('home')
    else:
        form = UserCreationForm()
    return render(request, 'usuarios/cadastro.html', {'form': form})

def home_view(request):
    return render(request, 'usuarios/home.html')
    
usuarios/urls.py
from django.urls import path
from django.contrib.auth.views import LoginView, LogoutView
from django.contrib.auth.decorators import login_required
from .views import cadastro_view, home_view
urlpatterns = [
    path('login/', LoginView.as_view(
        template_name='usuarios/login.html',
        redirect_authenticated_user=True
    ), name='login'),
    path('logout/', LogoutView.as_view(next_page='login'), name='logout'),

    path('cadastro/', cadastro_view, name='cadastro'),

    path('', login_required(home_view, login_url='login'), name='home'),
]
usuarios/templates/usuarios/base.html
<!doctype html>
<html lang="pt-br">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Gestão de Consultas</title>
    <link href="[https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css](https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css)" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        {% block content %}
        {% endblock %}
    </div>
    <script src="[https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js](https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js)"></script>
</body>
</html>

usuarios/templates/usuarios/login.html
{% extends 'usuarios/base.html' %}

{% block content %}
<h2>Login</h2>
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit" class="btn btn-primary">Entrar</button>
</form>
<p class="mt-3">Não tem uma conta? <a href="{% url 'cadastro' %}">Cadastre-se</a></p>
{% endblock %}

usuarios/templates/usuarios/cadastro.html
{% extends 'usuarios/base.html' %}

{% block content %}
<h2>Cadastro de Usuário</h2>
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit" class="btn btn-success">Cadastrar</button>
</form>
{% endblock %}

usuarios/templates/usuarios/home.html
{% extends 'usuarios/base.html' %}

{% block content %}
<h2>Bem-vindo, {{ user.username }}!</h2>
<p>Você está logado no sistema de gestão de consultas.</p>
<form method="post" action="{% url 'logout' %}">
    {% csrf_token %}
    <button type="submit" class="btn btn-danger">Sair (Logout)</button>
</form>
{% endblock %}



