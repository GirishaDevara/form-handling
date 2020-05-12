## User Registraion
### Model Creation
Model is a blue print for how we gonna store data (Creating the structure of tables).In other words, a model is a class which is used to contain essential fields and methods. Each model class maps to a single table in the database.
Let’s Create a Register model in `models.py` file

**`models.py`**
```python
from django.db import models


class Register(models.Model):
    gender_vals = [('Male', 'Male'), ('FeMale', 'FeMale')]
    firstName = models.CharField(max_length=100)
    lastName = models.CharField(max_length=100)
    emailId = models.EmailField(null=True)
    phoneNo = models.CharField(max_length=10)
    age = models.IntegerField(null=True)
    gender = models.CharField(max_length=10, choices=gender_vals)
    date_of_birth = models.DateField(null=True)
```
Each of our model fields has a related Django field type and field options. The Register model uses four different field types—CharField, DateTimeField, TextField and EmailField.

> **_NOTE:_** Don't forget to mention app name in INSTALLED_APPS list of settings.py file, if you created a new app
### Sync With database
Now that we’ve created the model, it’s time to add it to the database. To do this we need to open Command Promnt and run these two commands: 
-	python manage.py makemigrations 
-	python manage.py migrate
Your terminal output should look something like this:
<img src ="screenshots/migrations.JPG">

> **_NOTE:_** If you made any changes in the model you need to follow these two steps every time.

### Form Creation
A Form class describes a form and determines how it works and appears.In a similar way that a model class’s fields map to database fields, a form class’s fields map to HTML form `<input>` elements.

For the above Registraion model, which we could use to implement “Registraion” functionality on our website: 
First we need to create **froms.py** file in our app location and import Register model class from models.py

**`forms.py`**
```python
from django import forms
from .models import Register


class RegisterForm(forms.ModelForm):
    class Meta:
        model = Register
        fields = "__all__"
```
We need to import Django forms first (from django import forms) and our Register model (from .models import Register). Next, we have class Meta, where we tell Django which model should be used to create this form (model = Register). Finally, we can say which field(s) should end up in our form. In this scenario if we want only few fields then metion them in a list formate.

Here’s how the form data could be processed in the view that handles this form:

**`views.py`**
```python
from django.shortcuts import render
from django.http import HttpResponse
from .forms import RegisterForm


def register(request):
    form = RegisterForm()
    return render(request,'register.html',{'form':form})
```
Initially we are using get method, To check how from is working
> **_NOTE:_** Here we are using Bootstrap 4

Passing the context of form into register template that looks like this:

**`register.html`**
```html
<form action="" method="post">
    {% csrf_token %}
    {{ form }}
    <input type="submit" value="Submit">
</form>
```
> **_NOTE:_** Don’t forget that a form’s output does not include the surrounding `<form>` tags, or the form’s submit control. You will have to provide these yourself.

All you need to do to get your form into a template is to place the form instance into the template context. So if your form is called form in the context, {{ form }} will render its `<label>` and `<input>` elements appropriately.

This is a very basic form rendering, and as it is, Django will render it like this, with no style, just plain form fields: 
<img src ="screenshots/pic1.JPG">

Form is working properly but visuals are disappointing, Django provides some predefined ways to show forms in convenient manner. In templates following will modify the inputs as,

-   {{ form.as_p }} will render them wrapped in `<p>` tags
-   {{ form.as_table }} will render them as table cells wrapped in `<tr>` tags
-   {{ form.as_ul }} will render them wrapped in `<li>` tags
> **_NOTE:_** you’ll have to provide the surrounding `<table>` or `<ul>` elements yourself.

##### as_p()
*Form.as_p()*

as_p() renders the form as a series of `<p>` tags, with each `<p>` containing one field:
```html
<form action="" method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit" value="Submit">
</form>
```
Now the result, much better:
<img src ="screenshots/pic2.JPG">
##### as_table()
*Form.as_table()*


```
<form action="" method="post">
    {% csrf_token %}
		<table>
		{{ form.as_table }}
		</table>
    <input type="submit" value="Submit">
</form>
```
> **_NOTE:_** Add the {% csrf_token %} to every Django template you create that uses POST to submit data. This will reduce the chance of forms being hijacked by malicious users.
