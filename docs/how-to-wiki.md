# How to write on the internal wiki

Welcome! :star: If you've found your way here, chances are you're interested in contributing to this wiki or perhaps just exploring. If it's the former, congratulations – you're in the right place! :tada: 

If it's the latter, feel free to take a look around; you might discover something you enjoy. :mag_right: And who knows, by the end, you might find yourself inspired to contribute to this wiki as well. :writing_hand::books:

## Get started

To begin, head to the [wiki repository](https://github.com/DIOL-UniTN/InternalWiki) and follow the instructions outlined in the README to clone and set up the repository on your PC. Afterward, return here to explore the folder and file structure, grasp the basics of writing in markdown, and deploy your first guide on this internal wiki.


## File and Folder structure

This is the file and folder structure. Take a look at the `mkdocs.yml` file – it's the mastermind behind the wiki structure, allowing you to specify its skeleton. Next up is the `docs/` folder, home to all the markdown files. Feel free to add yours if the need arises. You'll also come across the `.github/workflows/` folder – just give it a pass, and don't mind the `.gitignore`, `README.md`, `LICENSE`, and `requirements.txt` files.


```text
.github/
    workflows/
        ci.yml
docs/
    index.md
    how-to-wiki.md
    server-guide/
        slurm.md
        ...
    ...
.gitignore
LICENSE
mkdocs.yml
README.md
requirements.txt
```

### The mkdocs.yml file

Delving into the `mkdocs.yml` file, pay particular attention to the `nav` section. Here, you'll have the opportunity to intricately design the structure of the tabs at the top of the wiki and fine-tune the navigation within them. This is where you can sculpt a user-friendly experience, ensuring a well-organized and seamless interface for your readers.

```yaml
nav:
  - Home: index.md
  - How to wiki: how-to-wiki.md
  - Server: 
    - server-guide/index.md
    - Slurm: server-guide/slurm.md
```

Upon examination of this code snippet, it becomes evident that we have three tabs – namely, "Home," "How to Wiki," and "Server." Notably, the "Server" tab reveals additional subpages underneath it. Delving into this tab provides the flexibility to choose a specific sub-topic for a more focused exploration

For instance, if you wish to include a page under the 'Server' tab, simply add a markdown file within the 'server-guide/' folder:

```text hl_lines="7"
...
docs/
    index.md
    how-to-wiki.md
    server-guide/
        slurm.md
        new-page.md
        ...
    ...
...
```

If you intend to introduce a new tab labeled 'Transportation' and populate it with two pages for guides on 'How to Sit on Bus No. 5' and 'Forecasting of Buses Deviation,' the updated code would be:
```yaml hl_lines="7 8 9"
nav:
  - Home: index.md
  - How to wiki: how-to-wiki.md
  - Server: 
    - server-guide/index.md
    - Slurm: server-guide/slurm.md
  - Transportation: 
    - How to sit on Bus No. 5: transportation/sit-on-five.md
    - Forecasting of Buses Deviation: transportation/bus-deviation.md
```
and the new file structure will be:
```text hl_lines="6 7 8"
docs/
    index.md
    how-to-wiki.md
    server-guide/
        ...
    tansportation/
        sit-on-five.md
        bus-deviation.md
    ...
```

## Markdown basics
Here, you'll discover fundamental markdown guidelines to commence your wiki contributions. Given the nature of likely contributions, such as guiding installations of libraries or ensuring code functionality, the emphasis will primarily be on effectively articulating code in markdown. For full documentation visit [mkdocs.org](https://www.mkdocs.org) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)


### Code blocks
Here the markdown code for a simple code block, where "language" is the actual language of the code snippet you're writing. 
````markdown
```language
bool get_buses_deviation(bool christmas_markets){
    if(christmas_markets){
        return true;
    }
    return false;
}
```
````
The rendered output using "C++" as language will look like this:
```c++
bool get_buses_deviation(bool christmas_markets){
    if(christmas_markets){
        return true;
    }
    return false;
}
```

If you want to highlight some lines of code, you'll have to add `hl_lines="line numbers"` like this:
````markdown
```c++ hl_lines="3 4"
bool get_buses_deviation(bool christmas_markets){
    if(christmas_markets){
        std::cout << "People are freezing in Piazza Fiera, ignoring the bus will never pass " << std::endl;
        send_help();
        return true;
    }
    return false;
}
```
````
Rendered output:
```c++ hl_lines="3 4"
bool get_buses_deviation(bool christmas_markets){
    if(christmas_markets){
        std::cout << "People are freezing in Piazza Fiera, ignoring the bus will never pass " << std::endl;
        send_help();
        return true;
    }
    return false;
}
```

You can also add the title and line numbers:
````markdown
```py title="transports.py" linenums="1"
def get_buses_deviation(christmas_markets):
    if(christmas_markets):
        return true
    return false
```
````

Rendered output:

```py title="transports.py" linenums="1"
def get_buses_deviation(christmas_markets):
    if(christmas_markets):
        return true
    return false
```

### Add tables
Here you can find the code snippet for creating tables. You can either write it in a pretty or raw way, the rendered result will always be the same:
```markdown title="Pretty"
| Bus stop             | Time Arrival  |
|----------------------|---------------|
| Povo Valoni          | 08:43         |
| Povo Facoltà scienze | 08:44         |
```
```markdown title="Raw"
| Bus stop | Time Arrival |
|----------|---------------|
| Povo Valoni | 08:43 |
| Povo Facoltà scienze | 08:44 |
```
Rendered output:

| Bus stop             | Time Arrival  |
|----------------------|---------------|
| Povo Valoni          | 08:43         |
| Povo Facoltà scienze | 08:44         |

### Add images
To incorporate images, simply upload them to the `docs/img/` folder. Feel free to organize your images into subfolders for better management. Subsequently, you can integrate the image into your content using the provided code, replacing 'your_image.jpeg' with the respective file name.

```html
<img alt="image placeholder" src="../img/your_image.jpeg" width='250'>
```

For example, you can find here an image of a cute capibara peluche to bright your day!

<img alt="Capibara" src="../img/capibara.jpeg" width='250'>

### Admonition
Additionally, you have the option to incorporate aesthetically styled admonitions. Feel free to scroll through the guide to explore various types available for enhancing the presentation of your content.
Here is the markdown code snippet:
```markdown
!!! note
    blablabla
```

Below you can find all the types rendered:

`note`
!!! note
    blablabla

`abstract`
!!! abstract
    blablabla

`tip`
!!! tip
    blablabla

`success`
!!! success
    blablabla

`question`
!!! question
    blablabla?

`warning`
!!! warning
    blablabla!

`failure`
!!! failure
    blablabla

`danger`
!!! danger
    blablabla

`bug`
!!! bug
    blablabla

`example`
!!! example
    blablabla

`quote`
!!! quote
    "blablabla"

## Deploy your contribution

Once you've finished your valuable contribution to this wiki, the next step is to deploy it to the site. Don't be scared, the process is straightforward! 😄

All you need to do is commit and push your new or updated files to the GitHub repository. In case you're not familiar with Git, you'll find the terminal commands below for your convenience. For obvious reasons edit the message "New guide on how to do things" with something more descriptive of what you've done.

```bash
git add .
git commit -m "New guide on how to do things"
git push origin main
```

Following the push to the main branch, a bit of magic will take place, and in no time, your modifications will be live. Simply refresh the page to admire your splendid work! 👏