# Internship Projects
## Python Project

### Introduction

I spent two weeks working as part of a development team on a large web application
built utilizing Django. Working on the project resulted in many [back-end](#back-end-stories) stories that I am proud of. I 
completed many stories of varying degrees of difficulty during my time on this project. Although the project
consisted largely of Python, I was also able to utilize my [front-end](#front-end-stories) skills with HTML, CSS, and JavaScript.
We used Azure DevOps for this project which allowed me to work with my peers continuously throughout the 
project and put a large focus on version control. I adhered to PEP8, Python's official style guide. I 
experienced and took part in our sprint planning kickoff as well as daily stand-ups and weekly code 
retrospectives to discuss what the team and myself had been working on, any roadblocks, and what we could 
improve on during the remaining time of our sprint. This was all crucial to ensure the team remained 
communicative and resulted in a seamless and efficient project completion. I am sure these skills in project 
management and team coding will be used again and again in my career.

### Back End Stories
---

* [Create Models](#create-models)
* [Create Forms](#create-forms)
* [Pagination](#pagination)
* [Edit Functions](#edit-functions)
* [Data Scraping](#data-scraping)


#### Create Models

I was tasked with creating models for a database of my creation.

```
class AlbumNames(models.Model):
    album_name = models.CharField(max_length=100, default="", blank=True, null=False)
    artist_name = models.CharField(max_length=60, default="", blank=True, null=False)
    date_album_released = models.DateField(("Date Album Released"), default=datetime.date.today)
    number_of_tracks = models.PositiveIntegerField(default=1, blank=True, null=False)
    genre = models.CharField(max_length=20, default="", blank=True, null=False)
    img_url = models.CharField(max_length=250, default="", blank=True, null=False)

    objects = models.Manager()

    def __str__(self):
        return self.album_name
```


#### Create Forms

Utilizing Crispy Forms allowed me to format the layout of my forms from the back end.

```
{% extends "MusicApp/MusicApp_base.html" %}
{% load static %}

{% load crispy_forms_tags %}
{% block title %}MusicApp{% endblock %}


{% block content %}
    <div class="input-forms">
        <h1>Add an Artist to MusicApp</h1>
        {% crispy form %}
    </div>
{% endblock %}
```

I created forms for user input to add items to my database. I also included a widget for a clickable calendar 
used for date selection from the user.

```
class AlbumNamesForms(forms.ModelForm):
    class Meta:
        model = AlbumNames
        fields = '__all__'
        widgets = {
            'date_album_released': forms.DateInput(attrs={'class ': 'form-styling', 'type': 'date'}),

        }

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.helper = FormHelper()
        self.helper.layout = Layout(
            Row(
                Column('album_name', css_class='form-group col-md-9 mb-0'),
                Column('number_of_tracks', css_class='form-group col-md-3 mb-0'),
                css_class='form-row'
            ),
            Row(
                Column('artist_name', css_class='form-group col-md-8 mb-0'),
                Column('date_album_released', css_class='form-group col-md-4 mb-0'),
            ),
            Row(
                Column('img_url', css_class='form-group col-md-12 mb-0'),
                css_class='form-row'
            ),
            Submit('submit', 'Save Album')
        )
```

#### Pagination

I took advantage of pagination to display a set number of entries to my user per page.

```
{% if artists.has_other_pages %}
  <ul class="pagination">
    {% if artists.has_previous %}
      <li><a href="?page={{ artists.previous_page_number }}">&laquo;</a></li>
    {% else %}
      <li class="disabled"><span>&laquo;</span></li>
    {% endif %}
    {% for i in artists.paginator.page_range %}
      {% if artists.number == i %}
        <li class="active"><span>{{ i }} <span class="sr-only"></span></span></li>
      {% else %}
        <li><a href="?page={{ i }}">{{ i }}</a></li>
      {% endif %}
    {% endfor %}
    {% if artists.has_next %}
      <li><a href="?page={{ artists.next_page_number }}">&raquo;</a></li>
    {% else %}
      <li class="disabled"><span>&raquo;</span></li>
    {% endif %}
  </ul>
{% endif %}
```

#### Edit Functions

I created a template where users could choose to edit previous items in the database.

```
def edit_artist(request, pk):
    artists = get_object_or_404(MusicalArtists, pk=int(pk))
    form = MusicalArtistsForm(data=request.POST or None, instance=artists)
    if request.method == 'POST':
        if form.is_valid():
            form2 = form.save(commit=False)
            form2.save()
            return redirect('MusicApp_artist_details', pk)
        else:
            print(form.errors)
    else:
        return render(request, 'MusicApp/MusicApp_edit_artist.html', {'artists': artists, 'form': form})

```

#### Data Scraping

I utilized BeautifulSoup to parse through html from another website and scrape data to display a list of items on a template of my own.

```
def bs_artist_chart(request):
    r = requests.get('https://www.rollingstone.com/charts/artists/')
    soup = bs(r.content, 'html.parser')
    titles = soup.find_all(class_='c-chart__table--title')
    artist_list = []
    for i in titles:
        paragraphs = i.find_all('p')
        for p in paragraphs:
            artist = p.text
            artist_list.append(artist)
    print(artist_list)
    context = {"artist_list": artist_list}
    return render(request, 'MusicApp/MusicApp_top_artists_chart.html', context)
```


### Front End Stories
---
* [JavaScript Modal](#javascript-modal)
* [CSS Styling](#css-styling)


#### JavaScript Modal

I created a function to allow users to delete items from the database. Upon selecting to delete an item, I created a pop up
JavaScript Modal to ask the user to confirm their decision to delete the item before action was made.

```
<div id="modal" class="modal">
    <!------Close modal button------->
  <span onclick="document.getElementById('modal').style.display='none'" class="close" title="Close Modal">&times;</span>
    <hr>
    <div class="modal_container">
        <h2>Delete Artist</h2>
        <p>Are you sure you want to delete {{ artists.artist_name }}?</p>

        <!----- Form to Delete Artist, will call Delete function ------>
        <form class="modal-content" method="POST" action="../Delete/">
              {% csrf_token %}
            <input class="delete_btn" type="submit" value="Delete">
        </form>
            <button class="btn" onclick="document.getElementById('modal').style.display='none'">Cancel</button>

    </div>
</div>
```

#### CSS Styling

I was having issues seeing my form labels and button text on top of my background image. I added CSS styling to include a 
blurred background to my forms and a hover effect on my buttons to make them fit in with the rest of my page styling.

```
/* ---------- Style Buttons ---------- */
button[type="button"] {
    flex-grow: 1;
    width: 10%;
    justify-content: right;
    text-align: center;
    display: inline-block;
    margin: 2% 2% 2% 2%;
    background-color: transparent;
    color: white;
    padding: 2px 0px 2px 0px;
    text-decoration: none;
    font-size: 14px;
    -webkit-transition-duration: 0.4s; /* Safari */
    transition-duration: 0.4s;
    cursor: pointer;
    border: 2px solid silver;
    border-radius: 5px;
}

button[type="button"]:hover {
    background-color: black;
    color: white;
    border: 2px solid #b3b3b3;
    box-shadow: 0 5px 15px rgba(0,0,0,0.3);
	transition: opacity 0.3s ease-in-out;
}



/* ------ Transparent Box around Forms ------- */
.input-forms {
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	width: 700px;
	height: 500px;
	padding: 80px 40px;
	background: rgba(0, 0, 0, 0.5);
}
```

*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#internship-projects)*


## MVC/MVVM Project

### Introduction

As a member of a development team, I worked on a full scale MVC/MVVM Web Application in C#, jQuery, CSS, HTML, and more. Working on a legacy codebase was a great learning opportunity for fixing bugs, refactoring code, and adding requested features. There were big changes that could have de-railed our timeline, but by utilizing Scrum and Azure DevOps, we were able to deliver time. I was able to prove that through research and collaboration, I could meet the client's needs. I worked on several back end stories that I am very proud of. Because much of the site had already been built, there were also a good deal of front end stories and UX improvements that needed to be completed, all of varying degrees of difficulty. 

Below are descriptions of the stories I worked on, along with code snippets and navigation links. 


### Back End Stories
---

* [Search Functionality](#search-functionality)
* [Animations](#animations)
* [Pop Up](#pop-up)
* [Styling](#styling)


#### Search Functionality

I was tasked with creating a search bar that would filter through sponsors for a theater company.

```
  // Filter table values based off text in search bar, refresh button undoes filter
    $(document).ready(function () {
        $("#searchFilter").on("keyup", function () {
            var searchText = $(this).val().toLowerCase();
            $("#sponsor-search .sponsor-index--row").filter(function () {
                $(this).toggle($(this).text().toLowerCase().indexOf(searchText) > -1)
            });
        });
        $('#clearSearch').click(function () {
            $("#searchFilter").val("");
            $("#searchFilter").trigger("keyup")
        });
    });
 
 ```
 
#### Animations
 
I utilized jQuery to create a slide-out panel to display edit and delete buttons as well as a details button upon mouseover of a sponsor logo.

```
    //slide out icons from bottom of cards on hover
    $('.sponsor-index--card').on('mouseenter', function () {
        $(this).find('.sponsor-index--card-text').slideDown(300);
    });
    //slide icons back up under bottom of cards when cursor no longer hovers
    $('.sponsor-index--card').on('mouseleave', function (event) {
        $(this).find('.sponsor-index--card-text').css({
            'display': 'none'
        });
    });
    
```

#### Pop Up

Upon clicking the details icon, a pop-up would appear.

```
    //Pop up for details icon
    function sponsorPop(div) {
    var popup = div.querySelector(".sponsor-index--PopUpText");
    if (popup) {
        popup.classList.toggle("show");
    }
}

```

The pop up looped through our database and displayed various information about the theater company's sponsors.

```
<div class="sponsor-index--row">
  <div class="col-12 col-md-6 col-lg-4 mb-4">
    <div class="sponsor-index--card">
	<a href="@item.Link" target="_blank"><img src="@Url.Action("DisplayPhoto", "Photo", new { id = item.PhotoId})" class="medium_size" title="@Html.DisplayFor(modelItem => 	   item.Name)" alt="Sponsor Logo Image" /></a>
	<div class="sponsor-index--card-body">
	    <div class="sponsor-index--card-text">
		<button class="sponsor-index--iconBtnMini" onclick="window.location.href ='@Url.Action("Edit", "Sponsors", new { id = item.SponsorId })'">
		    <i class="fa fa-edit fa-2x"></i>
		</button>
		<div class="sponsor-index--PopUp" onclick="sponsorPop(this)">
		    <button class="sponsor-index--iconBtnMini">
			<i class="fa fa-info-circle fa-2x"></i>
		    </button>
		    <span class="sponsor-index--PopUpText">
			<span class="sponsor-index--details">
			    <strong>Name:</strong><br /> @Html.DisplayFor(modelItem => item.Name)<br />
			    <strong>Dimensions</strong><br /> @Html.DisplayFor(modelItem => item.Photo.OriginalWidth) x @Html.DisplayFor(modelItem => item.Photo.OriginalHeight) 				(Width x Height)<br />
			    <strong>Link:</strong><br /> <a href="@item.Link" target="_blank">@Html.DisplayFor(modelItem => item.Link)</a>
			</span>
		    </span>
		</div>
		<button class="sponsor-index--iconBtnDelete" id="iconBtnDelete" onclick="window.location.href ='@Url.Action("Delete", "Sponsors", new { id = item.SponsorId })'">
		    <i class="fa fa-trash-alt fa-2x"></i>
		</button>
	    </div>

```

#### Styling

I utilized CSS to style the pop up.

```
/* Popup container */
.sponsor-index--PopUp {
    position: relative;
    display: inline-block;
    cursor: pointer;
}

/* The actual popup (appears on top) */
.sponsor-index--PopUp .sponsor-index--PopUpText {
    visibility: hidden;
    width: 160px;
    background-color: var(--light-color);
    color: var(--dark-color);
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI 		Symbol", "Noto Color Emoji";
    text-align: left;
    font-size: 0.75rem;
    border-radius: 6px;
    padding: 8px;
    position: absolute;
    z-index: 1;
    bottom: 125%;
    left: 50%;
    margin-left: -80px;
}

/*Popup arrow */
.sponsor-index--PopUp .sponsor-index--PopUpText::after {
    content: "";
    position: absolute;
    top: 100%;
    left: 50%;
    margin-left: -5px;
    border-width: 5px;
    border-style: solid;
    border-color: var(--light-color) transparent transparent transparent;
}

/* Toggle this class when clicking on the popup container (hide and show the popup) */
.sponsor-index--PopUp .show {
    visibility: visible;
    -webkit-animation: fadeIn 1s;
    animation: fadeIn 1s
}

/* Add animation (fade in the popup) */
/*@-webkit-keyframes fadeIn {
    from {
        opacity: 0;
    }

    to {
        opacity: 1;
    }
}

@keyframes fadeIn {
    from {
        opacity: 0;
    }

    to {
        opacity: 1;
    }
}*/

/* =========== END SPONSOR INDEX PAGE DETAILS POPUP */

```
---

### Other Skills Learned
* How to work on a team amongst other developers
* Collaborative problem solving
* Time Management - Sticking to deadlines
* How to navigate Azure DevOps
* Version Control


*Jump to: [Python Project](#python-project), [Page Top](#internship-projects)*
