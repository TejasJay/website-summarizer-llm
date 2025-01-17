
This Python code leverages multiple libraries and OpenAI's GPT model to fetch, parse, and summarize the content of a website. Below is a breakdown of the code and its functionality:

---

## 1. **Imports and Setup**

```python
import os
import requests
from bs4 import BeautifulSoup
from dotenv import load_dotenv
from openai import OpenAI
from IPython.display import Markdown, display
os: Provides functionality to interact with the operating system, such as retrieving environment variables.
requests: Used to send HTTP requests to websites and retrieve their content.
BeautifulSoup: A part of the bs4 package, it is used for parsing HTML and XML documents. It helps extract the content from the website.
dotenv: Loads environment variables from a .env file. This is useful for securely managing API keys.
openai: Interface to communicate with the OpenAI API to make calls to GPT models.
IPython.display: Used for displaying Markdown content in Jupyter Notebooks or IPython environments.
2. Loading the API Key
python
Copy
load_dotenv(override=True)
api_key = os.getenv('OPENAI_API_KEY')
load_dotenv(override=True): Loads environment variables from a .env file in the current directory. This is important for securely managing sensitive data, such as the OpenAI API key.
api_key = os.getenv('OPENAI_API_KEY'): Retrieves the OpenAI API key stored in the .env file.
3. Validating the API Key
python
Copy
if not api_key:
    print("No API key was found - please head over to the troubleshooting notebook in this folder to identify & fix!")
elif not api_key.startswith("sk-proj-"):
    print("An API key was found, but it doesn't start sk-proj-; please check you're using the right key - see troubleshooting notebook")
elif api_key.strip() != api_key:
    print("An API key was found, but it looks like it might have space or tab characters at the start or end - please remove them - see troubleshooting notebook")
else:
    print("API key found and looks good so far!")
The code checks if the API key is found, starts with the correct prefix (sk-proj-), and does not contain leading or trailing whitespace characters. If any of these conditions are violated, the code provides an error message for troubleshooting.
4. Initializing OpenAI
python
Copy
openai = OpenAI()
Creates an instance of the OpenAI API client to interact with GPT models.
5. Making an Initial Chat Request to OpenAI
python
Copy
message = 'This is my first message to openAI'
response = openai.chat.completions.create(model='gpt-4o-mini', messages = [{'role':'user', 'content':message}])
response.choices[0].message.content
Sends a simple message ("This is my first message to openAI") to the OpenAI API.
The response from the model is returned, and the content of the first choice is displayed.
6. Headers for Web Scraping
python
Copy
headers = {
 "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/117.0.0.0 Safari/537.36"
}
headers: This header mimics the behavior of a legitimate web browser, preventing the website from blocking the request by thinking it is coming from a bot.
7. Website Class
python
Copy
class Website:
    def __init__(self, url):
        self.url = url
        response = requests.get(url, headers=headers)
        soup = BeautifulSoup(response.content, 'html.parser')
        self.content = soup
        self.title = soup.title.string if soup.title else "No title found"
        
        for irrelevant in soup.body(["script", "style", "img", "input"]):
            irrelevant.decompose()

        self.text = soup.body.get_text(separator = "\n", strip=True)
Website class: This class is designed to represent a website and extract its relevant text content.
The __init__ method takes a URL, sends an HTTP request to retrieve the HTML content of the website, and then parses it using BeautifulSoup.
The title attribute stores the title of the website.
The text attribute stores the body content of the website with irrelevant elements (such as scripts, styles, images, and inputs) removed.
8. Creating Website Instances and Displaying Text
python
Copy
ed = Website("https://edwarddonner.com")
print(ed.text)

github = Website("https://www.github.com")
print(github.text)
Creates instances of the Website class for two websites, edwarddonner.com and github.com, and prints the cleaned-up text content of each website.
9. System Prompt for GPT Model
python
Copy
system_prompt = "You are an assistant that analyzes the contents of a website and provides a short summary, ignoring text that might be navigation related. Respond in markdown."
system_prompt: Defines the behavior of the GPT model. It instructs the model to analyze the contents of a website and generate a summary while ignoring irrelevant navigation text.
10. User Prompt Creation
python
Copy
def user_prompt_for(website):
    user_prompt = f"you are looking at the website titled {website.title} \n"
    user_prompt += "\nThe contents of this website is as follows; \
                    please provide a short summary of this website in markdown. \
                    If it includes news or announcements, then summarize these too.\n\n"
    user_prompt += website.text
    return user_prompt
user_prompt_for: Generates a prompt for the OpenAI model, asking it to summarize the content of a given website.
This includes the title of the website and its content, and asks for a markdown summary, including any news or announcements.
11. Messages for GPT Model
python
Copy
def messages_for(website):
    return [
        {'role':'system',  'content':system_prompt},
        {'role':'user', 'content':user_prompt_for(website)}
    ]
messages_for: Creates the complete set of messages to be sent to the OpenAI model, including both the system prompt and the user prompt.
12. Summarize Function
python
Copy
def summarize(url):
    website = Website(url)
    response = openai.chat.completions.create(
        model= 'gpt-4o-mini',
        messages= messages_for(website)
    )
    return response.choices[0].message.content
summarize: This function takes a website URL, creates a Website object for that URL, sends the relevant messages to the OpenAI model, and returns the generated summary in markdown format.
Conclusion:
This script combines web scraping (with BeautifulSoup) and the OpenAI GPT model to fetch and summarize website content. It uses the OpenAI API to generate readable summaries in markdown format, ignoring irrelevant navigation elements. This can be useful for building website content summarizers or scraping and processing web data automatically.