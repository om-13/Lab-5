# IT314: SOFTWARE ENGINEERING
# LAB: 5
## _Student ID: 202001276_
## _Name: OM CHALODIYA_

### Static Analysis using mypy for python files
### Link of the repo: https://github.com/om-13/DBMS_PROJECT.git
### Installing mypy library:
![1](https://user-images.githubusercontent.com/77343775/225916321-2d0e01e0-b49d-4ef1-b664-231db628cc2d.jpg)
### Python Code Used:
~~~
from django.shortcuts import render
from Olympics.models import Athlete,Leaderboard
from django.contrib import messages
from django.http import HttpResponse
from Olympics.forms import LeaderboardForms,AthleteForms
from django.db import connection


def HomePage(request):
    return render(request,'HomePage.html')

def showLeaderboard(request):
    showall=Leaderboard.objects.all()
    context = {
        'data': showall
    }
    return render(request,'showLeaderboard.html',context)

def sortLeaderboard(request):
    if request.method=="POST":
        if request.POST.get('Sort'):
            type=request.POST.get('Sort')
            sorted=Leaderboard.objects.all().order_by(type)
            context = {
                'data': sorted
            }
            return render(request,'sortLeaderboard.html',context)
    else:
        return render(request,'sortLeaderboard.html')

def insertLeaderboard(request):
    if request.method=="POST":
        if request.POST.get('country_id') and request.POST.get('country_name') and request.POST.get('gold_medals') and request.POST.get('silver_medals') and request.POST.get('bronze_medals') and request.POST.get('total_medals'):
            saverecord=Leaderboard()
            saverecord.country_id=request.POST.get('country_id') 
            saverecord.country_name=request.POST.get('country_name')
            saverecord.gold_medals=request.POST.get('gold_medals')
            saverecord.silver_medals=request.POST.get('silver_medals')
            saverecord.bronze_medals=request.POST.get('bronze_medals')
            saverecord.total_medals=request.POST.get('total_medals')
            allval=Leaderboard.objects.all()
            
            for i in allval:
                if int(i.country_id)==int(request.POST.get('country_id')):
                    messages.warning(request,'Country already exists....!');
                    return render(request,'insertLeaderboard.html')

            saverecord.save()
            messages.success(request,'Leaderboard '+saverecord.country_name+' is saved successfully!')
            return render(request,'insertLeaderboard.html')
    else:
            return render(request,'insertLeaderboard.html')

def editLeaderboard(request,id):
    editLeaderboardObj=Leaderboard.objects.get(country_id=id)
    context={
        "Leaderboard":editLeaderboardObj
    }
    return render(request,'editLeaderboard.html',context)

def updateLeaderboard(request,id):
    updateLeaderboard=Leaderboard.objects.get(country_id=id)
    form=LeaderboardForms(request.POST,instance=updateLeaderboard)
    if form.is_valid():
        form.save()
        messages.success(request,'Record updated successfully!')
        return render(request,'editLeaderboard.html',{"Leaderboard":updateLeaderboard})

def delLeaderboard(request,id):
    delLeaderboardObj=Leaderboard.objects.get(country_id=id)
    context={
        "Leaderboard":delLeaderboardObj
    }
    return render(request,'delLeaderboard.html',context)

def deletedLeaderboard(request,id):
    delLeaderboardObj=Leaderboard.objects.get(country_id=id)
    delLeaderboardObj.delete()
    showall=Leaderboard.objects.all()
    messages.success(request,'Record deleted successfully!')
    return render(request,'delLeaderboard.html',{"Leaderboard": delLeaderboardObj})


def showAthlete(request):
    showall=Athlete.objects.all()
    context = {
        'data': showall
    }
    return render(request,'showAthlete.html',context)

def insertAthlete(request):
    if request.method=="POST":
        if request.POST.get('athlete_id') and request.POST.get('athlete_name') and request.POST.get('gender') and request.POST.get('height') and request.POST.get('weight') and request.POST.get('age') and request.POST.get('sport') and request.POST.get('country'):
            saverecord=Athlete()
            saverecord.athlete_id=request.POST.get('athlete_id')
            saverecord.athlete_name=request.POST.get('athlete_name')
            saverecord.gender=request.POST.get('gender')
            saverecord.height=request.POST.get('height')
            saverecord.weight=request.POST.get('weight')
            saverecord.age=request.POST.get('age')
            saverecord.sport=request.POST.get('sport')
            saverecord.country=request.POST.get('country')

            allval=Athlete.objects.all()
            
            for i in allval:
                if int(i.athlete_id)==int(request.POST.get('athlete_id')):
                    messages.warning(request,'Athlete already exists....!');
                    return render(request,'insertAthlete.html')

            saverecord.save()
            messages.success(request,'Athlete '+saverecord.athlete_name+' is saved successfully!')
            return render(request,'insertAthlete.html')
    else:
            return render(request,'insertAthlete.html')

def sortAthlete(request):
    if request.method=="POST":
        if request.POST.get('Sort'):
            type=request.POST.get('Sort')
            sorted=Athlete.objects.all().order_by(type)
            context = {
                'data': sorted
            }
            return render(request,'sortAthlete.html',context)
    else:
        return render(request,'sortAthlete.html')

def editAthlete(request,id):
    editAthleteObj=Athlete.objects.get(athlete_id=id)
    context={
        "Athlete":editAthleteObj
    }
    return render(request,'editAthlete.html',context)

def updateAthlete(request,id):
    updateAthlete=Athlete.objects.get(athlete_id=id)
    form=AthleteForms(request.POST,instance=updateAthlete)
    if form.is_valid():
        form.save()
        messages.success(request,'Record updated successfully!')
        return render(request,'editAthlete.html',{"Athlete":updateAthlete})

def delAthlete(request,id):
    delCusObj=Athlete.objects.get(athlete_id=id)
    context={
        "Athlete":delCusObj
    }
    return render(request,'delAthlete.html',context)

def deletedAthlete(request,id):
    delCusObj=Athlete.objects.get(athlete_id=id)
    delCusObj.delete()
    showall=Athlete.objects.all()
    messages.success(request,'Record deleted successfully!')
    return render(request,'delAthlete.html',{"Athlete": delCusObj})

def runQueryLeaderboard(request):
    raw_query = "select * from leaderboard order by total_medals desc limit 3;"

    cursor = connection.cursor()
    cursor.execute(raw_query)
    alldata=cursor.fetchall()

    return render(request,'runQueryLeaderboard.html',{'data':alldata})

def runQueryAthlete(request):
    raw_query = "select * from athlete where age between 30 and 35;"
    cursor = connection.cursor()
    cursor.execute(raw_query)
    alldata=cursor.fetchall()

    return render(request,'runQueryAthlete.html',{'data':alldata})
~~~
