# Python-Live-Project

Introduction: 

As part of my internship for Prosper IT Consulting, I was part of a two week sprint, working in PyCharm on a Python Live project. This was an individual web application project focusing on a hobby or subject that interested the user. I worked with creating Models, Views, URLs, and front-end templates. My website was a database of soccer players and statistics. 


Front-End Templates:

SoccerApp_base.html:

      <!DOCTYPE html>
      <html lang="en" class="bg_img">
          <head>
              <meta charset="UTF-8">
              <title>{% block title %}{% endblock %}</title>
              <meta name="viewport" content="width=device-width, initial-scale=1">

               <!-- Bootstrap -->
              <link href="https://fonts.googleapis.com/css?family=Roboto" rel="stylesheet">
          <!--<link href="//fonts.googleapis.com/css?family=Lobster&subset=latin,latin-ext" rel="stylesheet" type="text/css">
              -->
              <!-- Custom -->
              <link rel="stylesheet" type="text/css" href="{% static '/SoccerApp/css/SoccerApp.css' %}">
          </head>

          <body>
              <div class="page_container">
                  {% include './SoccerApp_navbar.html' %}

                  {% block content %}{% endblock %}
              </div>
                  {% include "./SoccerApp_footer.html" %}
          </body>
      </html>


SoccerApp_index.html:

      {% extends 'SoccerApp/SoccerApp_base.html' %}

      {% block content %}

          <style>
              body {
                   background-image: url("../../static/SoccerApp/images/background.jpg");
                  }
          </style>
          <h1>Player:</h1>
                  <tr>
                      <th>Name</th>
                      <th>Position</th>
                      <th>Team</th>
                      <th>Salary</th>
                  </tr>
      {% for add in adds %}
                  <tr>
                      <td>{{add.Name}}</td>
                      <td>{{add.Position}}</td>
                      <td>{{add.Team}}</td>
                      <td>{{add.Salary}}</td>
                      <td><a href="{{ add.id }}/details">More Info</a></td>
                  </tr>

      {% endfor %}

      </table>

      {% endblock %}


SoccerApp_navbar.html

      {% load static %}
      <ul class="navbar">
          <li><a href="{% url 'SoccerAppHome' %}">Home</a></li>
          <li><a href="{% url 'Submit' %}">Statistics</a>
          <li><a href="{% url 'add' %}">Add Players</a></li>
      </ul>
      
from django.shortcuts import render, redirect, get_object_or_404
from .forms import PlayerForm
from .models import player
from django.http import Http404


def soccer_home(request):
    return render(request, 'SoccerApp/SoccerApp_home.html')

def Addplayer(request):
    #create form
    form = PlayerForm(request.POST or None)

    #check if form is valid
    if request.method == 'POST':
        if form.is_valid():
            form.save()
            return redirect('Submit')

    else:

        context = {
            'form': form
        }
        # redirect to a new url:
        return render(request, 'SoccerApp/SoccerApp_addplayer.html', context)

SoccerApp_delete.html:

      def Submit(request):
          adds = player.objects.all()
          context = {'adds': adds}
          return render(request, 'SoccerApp/SoccerApp_index.html', context)


      def details(request, pk):
          try:
              Adds = player.objects.get(pk=pk)
          except player.DoesNotExist:
              raise Http404('Player not available')
          return render(request, 'SoccerApp/SoccerApp_details.html', {'Adds': Adds})


      def delete(request, pk):
          pk = int(pk)
          add = get_object_or_404(player, pk=pk)
          if request.method == 'POST':
              add.delete()
              return redirect('Submit')
          context = {'add': add}
          return render(request, 'SoccerApp/SoccerApp_delete.html', context)


      def edit(request, pk):
          pk = int(pk)
          add = get_object_or_404(player, pk=pk)
          if request.method == 'POST':
              form = PlayerForm(request.POST, instance=add)
              if form.is_valid():
                  add = form.save()
                  add.save()
                  return redirect('Details', pk=add.pk)
          else:
              form = PlayerForm(instance=add)
          context = {
              'form': form, 'pk': pk
           }
          return render(request, 'SoccerApp/SoccerApp_edit.html', context)

models.py:

      from django.db import models

      #Create Model
      POS_CHOICES = [
          ('Forward', 'Forward'),
          ('Midfielder', 'Midfielder'),
          ('Defender', 'Defender'),
          ('Goalkeeper', 'Goalkeeper'),
      ]


      class player(models.Model):
          Name = models.CharField(max_length=50, default="")
          Position = models.CharField(max_length=10, choices=POS_CHOICES, default="")
          Team = models.CharField(max_length=60, default="")
          Salary = models.IntegerField(default="")

          objects = models.Manager()

          def __str__(self):
              return self.Name


views.py:
  
      from django.shortcuts import render, redirect, get_object_or_404
      from .forms import PlayerForm
      from .models import player
      from django.http import Http404


      def soccer_home(request):
          return render(request, 'SoccerApp/SoccerApp_home.html')

      def Addplayer(request):
          #create form
          form = PlayerForm(request.POST or None)

          #check if form is valid
          if request.method == 'POST':
              if form.is_valid():
                  form.save()
                  return redirect('Submit')

          else:

              context = {
                  'form': form
              }
              # redirect to a new url:
              return render(request, 'SoccerApp/SoccerApp_addplayer.html', context)


      def Submit(request):
          adds = player.objects.all()
          context = {'adds': adds}
          return render(request, 'SoccerApp/SoccerApp_index.html', context)


      def details(request, pk):
          try:
              Adds = player.objects.get(pk=pk)
          except player.DoesNotExist:
              raise Http404('Player not available')
          return render(request, 'SoccerApp/SoccerApp_details.html', {'Adds': Adds})


      def delete(request, pk):
          pk = int(pk)
          add = get_object_or_404(player, pk=pk)
          if request.method == 'POST':
              add.delete()
              return redirect('Submit')
          context = {'add': add}
          return render(request, 'SoccerApp/SoccerApp_delete.html', context)


      def edit(request, pk):
          pk = int(pk)
          add = get_object_or_404(player, pk=pk)
          if request.method == 'POST':
              form = PlayerForm(request.POST, instance=add)
              if form.is_valid():
                  add = form.save()
                  add.save()
                  return redirect('Details', pk=add.pk)
          else:
              form = PlayerForm(instance=add)
          context = {
              'form': form, 'pk': pk
           }
          return render(request, 'SoccerApp/SoccerApp_edit.html', context)

urls.py:

        from django.urls import path
        from . import views

        urlpatterns = [
            path('', views.soccer_home, name='SoccerAppHome'),
            path('add', views.Addplayer, name='add'),
            path('Submit', views.Submit, name='Submit'),
            path('<int:pk>/details/', views.details, name='Details'),
            path('<int:pk>/delete/', views.delete, name='delete'),
            path('<int:pk>/edit/', views.edit, name='edit'),
        ]
        
        
Reflection:

My two week sprint creating a web application through Python was a great experience. I learned how a project can be created and finished, and how different aspects of the front and back end work together to develop content. 
