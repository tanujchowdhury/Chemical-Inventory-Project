# Chemical Inventory Project

## Introduction
During my time at the Tech Academy, I worked with a group of peers to make a full-stack web-app using Django that could connect to a database and had full CRUD functionality. We had opportunities to work on front-end and back-end stories, with varying degrees of difficulty. Each of us worked individually on our separate applications, but the apps were all integrated into the same legacy codebase. This project was a great opportunity to learn about the MVC design philopsphy and work as part of a team using AGILE methodolgy. The topic I chose was a chemical invetory application a lab manager might use to keep track of their lab's in-house materials. The app also connects to an API and includes a web-scraper. Over the two-week sprint I utilized project management skills that I can use in my futre projects. 

Below are the descriptions of the major stories I worked on with some code snippets

## Stories
* [CRUD Pages](#CRUD-Pages)
* API
* Web Scraping

### CRUD Pages
The create, read, details, update, and delete pages used HTTP GET/POST methods to display content on the page. Below are the methods in Views to accomplish each task. 

```
def inventory_create(request):
    form = MaterialForm(data=request.POST or None)
    if request.method == "POST":
        if form.is_valid():
            form.save()
            return redirect('../read')
    content = {'form': form}
    return render(request, 'Inventory/Inventory_create.html', content)

def inventory_read(request):
    material = Material.Materials.all()
    content = {'material': material}
    return render(request, 'Inventory/Inventory_read.html', content)

def inventory_details(request, pk):
    material = get_object_or_404(Material, pk=pk)
    content = {'material': material}
    return render(request, 'Inventory/Inventory_details.html', content)

def inventory_update(request, pk):
    material = get_object_or_404(Material, pk=pk)
    form = MaterialForm(data=request.POST or None, instance=material)
    if request.method == 'POST':
        if form.is_valid():
            form.save()
            return redirect('../../read')
    content = {'form': form, 'material': material}
    return render(request, 'Inventory/inventory_update.html', content)

def inventory_delete(request, pk):
    material = get_object_or_404(Material, pk=pk)
    if request.method == 'POST':
        material.delete()
        return redirect('../../read')
    content = {'material': material}
    return render(request, 'Inventory/inventory_delete.html', content)
```

### API
Although not related to chemical inventory, my app connects to an API, parses through the JSON, and displays the current temperature in San Jose, CA. Below is the function in Views.

```
def inventory_api(request):
    url = "https://yahoo-weather5.p.rapidapi.com/weather"

    querystring = {"location": "san jose", "format": "json", "u": "f"}

    headers = {
        "X-RapidAPI-Host": "yahoo-weather5.p.rapidapi.com",
        "X-RapidAPI-Key": "aa3d3af1d8mshe07b323c70b5c20p174de6jsn68e2b0bf2a3b"
    }

    response = requests.request("GET", url, headers=headers, params=querystring)

    api_info = json.loads(response.text)
    temp_int = api_info["current_observation"]["condition"]["temperature"]
    current_temperature = str(api_info["current_observation"]["condition"]["temperature"]) + ' \N{DEGREE SIGN}F'
    content = {"current_temperature": current_temperature, "temp_int": temp_int}
    return render(request, 'Inventory/inventory_api.html', content)
```

### Web Scraping
Web scraping done with Beautiful Soup. The HTML page is then parsed and the content in displayed on the web-page. Here is the function is Views.

```
def inventory_bs(request):
    page = requests.get("https://www.crummy.com/software/BeautifulSoup/bs4/doc/")
    soup = BeautifulSoup(page.content, 'html.parser')
    info = soup.find_all('p')[0].get_text()
    content = {"info": info}
    return render(request, 'Inventory/inventory_bs.html', content)
```
