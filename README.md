# IT314: SOFTWARE ENGINEERING
# LAB: 5
## _Student ID: 202001276_
## _Name: OM CHALODIYA_

## Static Analysis using mypy for python files
### Repo link: https://github.com/om-13/DBMS_PROJECT.git
### Installing mypy library:
![image](https://user-images.githubusercontent.com/77343775/227467679-8665bdf1-df2f-4e62-b73f-689e6ea406df.png)

### Python Code:
```ruby
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
```

### Error 1: 
Running above code and reviewing import errors:
![3](https://user-images.githubusercontent.com/77343775/227481005-2859e15f-cf5f-499c-b659-32d889bd6967.png)

### Error 2: 
Detecting and correcting indentation error:
![4](https://user-images.githubusercontent.com/77343775/227481167-cb53271c-3a1d-454f-bf24-6b50d833ff04.png)

### Error 3: 
Detecting and correcting syntax error:
![5](https://user-images.githubusercontent.com/77343775/227481214-f062a035-4683-4c8c-a57d-0ef191c9ba25.png)

### Error 4: 
Detecting and correcting syntax error:
![6](https://user-images.githubusercontent.com/77343775/227481279-3ea4d007-1dbc-4379-8e5c-4b34f70a12bb.png)

### Error 5: 
Detecting and correcting syntax error:
![7](https://user-images.githubusercontent.com/77343775/227481337-7ccbd5c7-3f53-4418-aedf-3d14febebc49.png)

### Error 6: 
Detecting and correcting name not defined error:
![8](https://user-images.githubusercontent.com/77343775/227481366-7714c409-b11d-4bd0-843e-f0685cab50bc.png)

### Error 7: 
Detecting and correcting parent module not found error:
![9](https://user-images.githubusercontent.com/77343775/227481430-247464c3-dee3-4f01-9188-064cd6bcfb33.png)
