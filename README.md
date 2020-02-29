# django 회원가입기능 만들기
<br><br>

**model.py** 

    from django.contrib.auth.models import User
    
    class Post(models.Model):
      author = models.ForeignKey(User, on_delete=models.CASCADE)
      	
<br><br>

**views.py**

    from django.shortcuts import render, redirect
    from django.contrib import messages
    from .forms import UserRegisterForm


    def register(request):
        if request.method == 'POST':
            form = UserRegisterForm(request.POST)
            if form.is_valid():
                form.save()
                # is_valid() 를 통해서 검증에 통과한 값은 cleaned_data 변수명으로 사전타입 으로 제공된다.
                username = form.cleaned_data.get('username')
                messages.success(request, f'{username} 계정이 생성되었습니다!')
                return redirect('/')
        else:
            form = UserRegisterForm()
        return render(request, 'users/register.html', {'form': form})
        
<br><br>    

**urls.py**
    
    # https://xxx.xxxx /register
    
    from django.contrib import admin
    from django.urls import path, include
    from users import views as user_views

    urlpatterns = [
        ''' 
        path('register/', user_views.register, name='register'),

    ]
        
 <br><br> 
 
 **forms.py**
 
    from django import forms
    from django.contrib.auth.models import User
    from django.contrib.auth.forms import UserCreationForm


    class UserRegisterForm(UserCreationForm):
        email = forms.EmailField()

    class Meta:
        model = User
        fields = ['username', 'email', 'password1', 'password2']
        
<br><br>
**base.html**
    
    # 회원가입이 성공할시 message.tags 활성화
    
    {% if messages %}
      {% for message in messages %}
        <div class="alert alert-{{ message.tags }}">
          {{ message }}
        </div>
      {% endfor %}
    {% endif %}
    {% block content %}{% endblock content %}
    

<br><br>
**register.html**

    {% extends "blog/base.html" %}
    {% load crispy_forms_tags %}  # crispy_forms_tags 추가
    {% block content %}
        <div class="content-section">
            <form method="post">
                {% csrf_token %}
                <fieldset class="form-group">
                    <legend class="border-bottom mb-4">회원가입</legend>
                    {{ form|crispy }}    # form|crispy  추가 
                </fieldset>
                <div class="form-group">
                    <button class="btn btn-outline-info" type="submit">가입</button>
                </div>
            </form>
            <div class="border-top pt-3">
                <small class="text-muted">이미 계정이 있으신가요? <a href="#" class="ml-2">로그인</a></small>
            </div>
        </div>
    {% endblock content %}
    
 <br><br>
 
**crispy install**

    1. pip install django-crispy-forms
    
    2. setting.py
    
    INSTALLED_APPS = (
    ...
    'crispy_forms',
    )
    
    CRISPY_TEMPLATE_PACK = 'bootstrap4'  # 맨 밑에 추가
    
    

