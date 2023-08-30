# Wordgets: A word memory desktop gadget



### 

[简体中文](https://github.com/leaffeather/wordgets/blob/main/README_schinese.md)


![cover](https://github.com/leaffeather/images/blob/main/wordgets_cover.png?raw=true)

**Update log:**

v1.0.3: (2023/8/30)

(1) Solve Know issues 4 by mandatory account logout 

v1.0.2: (2023/8/30)

(1) Fix some bugs in the code. Found a bug(refer to Known issues 4)

v1.0.1: (2023/8/29)

(1) Fix some erroneous message dialogs. Found a bug(refer to Known issues 3)

---
1.  Introduction

    Wordgets is a word memory app based on [Beeware](https://beeware.org/) -- a cross-platform, Python native toolkit. It has following features:
    - **Support custom backend**. Attention: users <u>must follow the local network laws and regulations made up by corresponding network service providers</u> when using the backend to get certain web services.
    - **Support cross-device and cross-platform synchonization via Baidu NetDisk**. 
    - **Support autoplay of the audio and video**.
    
    Supported platforms:
    - Windows >= 10
    - Android >= 10
    - MacOS >= 10.10
2.  Usage
    1. About the word list
    
        The word list should be a .xlsx Excel workbook with only one worksheet. The first row saves the column names and then you can put each words and their relative contents row by row. It is recommended to place a maximum of approximately 4000 words in each word list, otherwise there may be obvious lag during the use of the program. Enabling automatic reading should use a method such as `<audio src="[audio address]" autoplay></audio>`.
    2. About the template card
    
        This part requires little programming knowledge.
        You can specify one or two kinds of the single-sided or double-sided card for each word list. And each card side contains a frontend .html template webpage and a backend .py python script. Specifically:
        1. template webpage (for all supported platforms)
       
            Create a HTML and use fields wrapped in two curly braces like field *word* by `{{word}}`, field *explanation* by `{{explanation}}`. If you want to embed HTML code generated by python script, you need use fields like field *formula* by `{{formula | safe}}`.
        
        2. python script (for Windows/Android)
            ```python
            exec("from jinja2 import Environment, FileSystemLoader",globals())  # ** Do not modify **
            exec("from bs4 import BeautifulSoup",globals())   # Import a package you want by this method
            
            # The left is the field and the right corresponds to the alphabetical column number in Excel
            word = A 
            explanation = B 
            
            searchpath = folder_path                                            # ** Do not modify ** 
            name = file_name                                                    # ** Do not modify ** 
            env = Environment(loader=FileSystemLoader(searchpath=searchpath))   # ** Do not modify ** 
            template = env.get_template(name=name)                              # ** Do not modify ** 
            
            # You can customize some codes here
            formula = "<div id="raw-formula"> \(\int_a^b f(x)dx\) </div>"
            
            # You only need to modify the content within the parentheses, 
            # copy the fields twice, add = in the middle, and remember to add the comma
            output = template.render(word = word, 
                                     explanation = explanation,
                                     formula = formula)
            ```
        3. python script (for MacOS)
            ```python
            exec("from flask import Flask, render_template", globals())         # ** Do not modify **
            exec("from bs4 import BeautifulSoup",globals())   # Import a package you want by this method
            
            # The left is the field and the right corresponds to the alphabetical column number in Excel
            word = A 
            explanation = B 
            
            searchpath = folder_path                                            # ** Do not modify **
            name = file_name                                                    # ** Do not modify **
            app = Flask(__name__, template_folder = searchpath)                 # ** Do not modify **
            
            # You can customize some codes here
            formula = "<div id="raw-formula"> \(\int_a^b f(x)dx\) </div>"
            
            @app.route('/')                                                     # ** Do not modify **
            def index():                                                        # ** Do not modify **
                global word, explanation, formula # Place all fields you use after global,
                                                  # with a comma in the middle of every twos
                
                # Do not change the first line.
                # Copy each field you use twice later, add = in the middle,
                # and don't forget to add the comma.
                return render_template(template_name_or_list = name, 
                                       word = word, 
                                       explanation = explanation,
                                       formula = formula)
                                
            app.run(debug = False, use_reloader = False)                        # ** Do not modify **
            ```
            
    3. How to import cards or word lists
    
        If you are using a Windows or a MacOS, you can simply fill the text input with absolute path of the target file. The app will always interact with these files directly, so you should not make changes on them.
        To Android, things become more complex. Please follow these steps:
        1. Your phone and your Windows computer should be in the same LAN.
        2. Download the already configured nginx and unzip it. 
        3. Place your wordlists and cards into directory `WWW\` of nginx. Run `nginx.exe`. As it does not generate any windows, just make sure you run it.
        4. Open `cmd` and enter `ipconfig` and press ENTER key. Find your computer ip in LAN.
        5. Import the files via `http://[Your IP Address]:8080/[File name].[Its extension name]`
    4. How to use synchonization
    
        Please sign up a Baidu account and login Baidu NetDisk at least once. Then, follow these steps: 
        1. open [ Baidu NetDisk Open Platform](https://pan.baidu.com/union); 
        2. click [Apply to join] in the upper right corner and finish the application; 
        3. click [Console] and create an app; 
        4. view the details of the app and copy [AppKey] to the corresponding text input in the login procedure in wordgets.
        
        Attention:
        1. You'd better not open the software until having synchonized at other devices.
        2. You should terminate the app when you don't want to continue to remember any more.
        3. If synchonization is failed and a message tells you that there is no network connection, it might be an outcome of the running of the network proxy app like Clash.
        4. Wordgets will create a directory `wordgets_sync` on your Baidu NetworkDisk, don't move! Unless you plan to reset the synchronization database.

3.  Known issues
    1.  [On windows] The scroll bars in Settings are dysfunctional. You can stretch the window for complete display.
    2.  [On macOS] The widgets cannot adjust automatically.
    3.  [On macOS] If you are using an network proxy or the network is disconnected, the question dialog will not not allow you to make any choices. Please fix it and restart the app.
    4.  ~~[On macOS] If you want to change the wordlists or cards on other devices, you should firstly logout your Baidu account and then change. Otherwise, you may encounter an frozen error dialog.~~
