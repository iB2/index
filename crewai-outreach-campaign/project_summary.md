# instructions\enterprise_solutions_framework.md

```md
# Strategy and Innovation Framework for Enterprise Solutions ## Introduction When addressing enterprises, the focus should be on strategic partnership, innovation, and how your solutions integrate seamlessly with existing systems to drive efficiency and innovation. ## Key Points to Address - **Strategic Partnership:** Convey the value of a long-term partnership. - **Integration:** Highlight the ease of integration with their current systems. - **Innovation:** Showcase how your solutions contribute to driving innovation within their organization. ## Template Message Dear [Name], In today's fast-paced business environment, it's more important than ever to have partners who understand the breadth and depth of enterprise challenges. [Your Company] is committed to being that partner for [Enterprise Name]. Our [Product/Service] integrates seamlessly with your existing infrastructure, providing [Key Benefit] and supporting your innovation goals. We're looking forward to discussing a strategic partnership and how we can support [Enterprise Name] in achieving its objectives. Sincerely, [Your Name]
```

# instructions\small_business_engagement.md

```md
# Customer Engagement Guide for Small Businesses ## Introduction For small businesses, personal touch and understanding local needs are paramount. Your message should reflect an understanding of their market, the challenges they face, and how your solutions make their daily operations smoother and more efficient. ## Key Points to Address - **Personalization:** Show that you understand their specific business needs. - **Efficiency:** Highlight how your solutions can streamline operations. - **Community:** Emphasize your commitment to supporting local businesses. ## Template Message Hello [Name], As a local business owner, your dedication to [specific aspect of their business, e.g., providing excellent customer service, offering high-quality products] truly stands out. At [Your Company], we offer solutions that can help businesses like [Business Name] become even more efficient and effective. [Describe a specific feature of your product/service and how it solves a problem they face]. We would love to discuss how we can be part of your success story. Warm regards, [Your Name]
```

# instructions\tech_startups_outreach.md

```md
# Personalized Outreach Instructions for Tech Startups ## Introduction When engaging with tech startups, it's crucial to emphasize innovation, scalability, and how your solutions can aid in their growth and technological advancement. Startups are often looking for partners who understand their unique challenges and can offer flexible, cutting-edge solutions. ## Key Points to Address - **Innovation:** Highlight how your products or services are at the forefront of technology. - **Scalability:** Discuss how your solutions can grow with their business. - **Support:** Emphasize the support and resources you provide, which are critical for startups. ## Template Message Dear [Name], Congratulations on [Recent Achievement/News]! At [Your Company], we're excited about the innovative work you're doing at [Startup Name]. Our [Product/Service] is designed with tech startups like yours in mind, offering [Key Feature] to help you [Benefit]. We understand the importance of scalability and flexibility in your journey. [Insert how your solution can be tailored to their current and future needs]. Let's explore how we can support [Startup Name]'s growth together. Best, [Your Name]
```

# outreach.ipynb

```ipynb
{ "cells": [ { "cell_type": "code", "execution_count": null, "metadata": {}, "outputs": [], "source": [ "\n", "!pip install crewai==0.28.8 crewai_tools==0.1.6 langchain_community==0.0.29\n", "\n", "# Warning control\n", "import warnings\n", "warnings.filterwarnings('ignore')\n", "\n", "\n", "from crewai import Agent, Task, Crew\n", "\n", "import os\n", "from utils import get_openai_api_key, pretty_print_result\n", "from utils import get_serper_api_key\n", "​\n", "openai_api_key = get_openai_api_key()\n", "os.environ[\"OPENAI_MODEL_NAME\"] = 'gpt-4o-mini'\n", "os.environ[\"SERPER_API_KEY\"] = get_serper_api_key()\n", "\n", "\n", "sales_rep_agent = Agent(\n", " role=\"Sales Representative\",\n", " goal=\"Identify high-value leads that match \"\n", " \"our ideal customer profile\",\n", " backstory=(\n", " \"As a part of the dynamic sales team at CrewAI, \"\n", " \"your mission is to scour \"\n", " \"the digital landscape for potential leads. \"\n", " \"Armed with cutting-edge tools \"\n", " \"and a strategic mindset, you analyze data, \"\n", " \"trends, and interactions to \"\n", " \"unearth opportunities that others might overlook. \"\n", " \"Your work is crucial in paving the way \"\n", " \"for meaningful engagements and driving the company's growth.\"\n", " ),\n", " allow_delegation=False,\n", " verbose=True\n", ")\n", "lead_sales_rep_agent = Agent(\n", " role=\"Lead Sales Representative\",\n", " goal=\"Nurture leads with personalized, compelling communications\",\n", " backstory=(\n", " \"Within the vibrant ecosystem of CrewAI's sales department, \"\n", " \"you stand out as the bridge between potential clients \"\n", " \"and the solutions they need.\"\n", " \"By creating engaging, personalized messages, \"\n", " \"you not only inform leads about our offerings \"\n", " \"but also make them feel seen and heard.\"\n", " \"Your role is pivotal in converting interest \"\n", " \"into action, guiding leads through the journey \"\n", " \"from curiosity to commitment.\"\n", " ),\n", " allow_delegation=False,\n", " verbose=True\n", ")\n", "\n", "\n", "from crewai_tools import DirectoryReadTool, \\\n", " FileReadTool, \\\n", " SerperDevTool\n", "directory_read_tool = DirectoryReadTool(directory='./instructions')\n", "file_read_tool = FileReadTool()\n", "search_tool = SerperDevTool()\n", "\n", "\n", "from crewai_tools import BaseTool\n", "\n", "\n", "class SentimentAnalysisTool(BaseTool):\n", " name: str =\"Sentiment Analysis Tool\"\n", " description: str = (\"Analyzes the sentiment of text \"\n", " \"to ensure positive and engaging communication.\")\n", " \n", " def _run(self, text: str) -> str:\n", " # Your custom code tool goes here\n", " return \"positive\"\n", "sentiment_analysis_tool = SentimentAnalysisTool()\n", "\n", "\n", "lead_profiling_task = Task(\n", " description=(\n", " \"Conduct an in-depth analysis of {lead_name}, \"\n", " \"a company in the {industry} sector \"\n", " \"that recently showed interest in our solutions. \"\n", " \"Utilize all available data sources \"\n", " \"to compile a detailed profile, \"\n", " \"focusing on key decision-makers, recent business \"\n", " \"developments, and potential needs \"\n", " \"that align with our offerings. \"\n", " \"This task is crucial for tailoring \"\n", " \"our engagement strategy effectively.\\n\"\n", " \"Don't make assumptions and \"\n", " \"only use information you absolutely sure about.\"\n", " ),\n", " expected_output=(\n", " \"A comprehensive report on {lead_name}, \"\n", " \"including company background, \"\n", " \"key personnel, recent milestones, and identified needs. \"\n", " \"Highlight potential areas where \"\n", " \"our solutions can provide value, \"\n", " \"and suggest personalized engagement strategies.\"\n", " ),\n", " tools=[directory_read_tool, file_read_tool, search_tool],\n", " agent=sales_rep_agent,\n", ")\n", "\n", "\n", "personalized_outreach_task = Task(\n", " description=(\n", " \"Using the insights gathered from \"\n", " \"the lead profiling report on {lead_name}, \"\n", " \"craft a personalized outreach campaign \"\n", " \"aimed at {key_decision_maker}, \"\n", " \"the {position} of {lead_name}. \"\n", " \"The campaign should address their recent {milestone} \"\n", " \"and how our solutions can support their goals. \"\n", " \"Your communication must resonate \"\n", " \"with {lead_name}'s company culture and values, \"\n", " \"demonstrating a deep understanding of \"\n", " \"their business and needs.\\n\"\n", " \"Don't make assumptions and only \"\n", " \"use information you absolutely sure about.\"\n", " ),\n", " expected_output=(\n", " \"A series of personalized email drafts \"\n", " \"tailored to {lead_name}, \"\n", " \"specifically targeting {key_decision_maker}.\"\n", " \"Each draft should include \"\n", " \"a compelling narrative that connects our solutions \"\n", " \"with their recent achievements and future goals. \"\n", " \"Ensure the tone is engaging, professional, \"\n", " \"and aligned with {lead_name}'s corporate identity.\"\n", " ),\n", " tools=[sentiment_analysis_tool, search_tool],\n", " agent=lead_sales_rep_agent,\n", ")\n", "\n", "\n", "crew = Crew(\n", " agents=[sales_rep_agent, \n", " lead_sales_rep_agent],\n", " \n", " tasks=[lead_profiling_task, \n", " personalized_outreach_task],\n", " \n", " verbose=2,\n", " memory=True\n", ")\n", "\n", "inputs = {\n", " \"lead_name\": \"DeepLearningAI\",\n", " \"industry\": \"Online Learning Platform\",\n", " \"key_decision_maker\": \"Andrew Ng\",\n", " \"position\": \"CEO\",\n", " \"milestone\": \"product launch\"\n", "}\n", "​\n", "\n", "result = crew.kickoff(inputs=inputs)\n", "\n", "\n", "from IPython.display import Markdown\n", "Markdown(result)" ] } ], "metadata": { "kernelspec": { "display_name": "base", "language": "python", "name": "python3" }, "language_info": { "name": "python", "version": "3.12.7" } }, "nbformat": 4, "nbformat_minor": 2 }
```

# utils.py

```py
# Add your utilities or helper functions to this file.

import os
from dotenv import load_dotenv, find_dotenv

# these expect to find a .env file at the directory above the lesson.                                                                                                                     # the format for that file is (without the comment)                                                                                                                                       #API_KEYNAME=AStringThatIsTheLongAPIKeyFromSomeService
def load_env():
    _ = load_dotenv(find_dotenv())

def get_openai_api_key():
    load_env()
    openai_api_key = os.getenv("OPENAI_API_KEY")
    return openai_api_key

def get_serper_api_key():
    load_env()
    serper_api_key = os.getenv("SERPER_API_KEY")
    return serper_api_key


# break line every 80 characters if line is longer than 80 characters
# don't break in the middle of a word
def pretty_print_result(result):
  parsed_result = []
  for line in result.split('\n'):
      if len(line) > 80:
          words = line.split(' ')
          new_line = ''
          for word in words:
              if len(new_line) + len(word) + 1 > 80:
                  parsed_result.append(new_line)
                  new_line = word
              else:
                  if new_line == '':
                      new_line = word
                  else:
                      new_line += ' ' + word
          parsed_result.append(new_line)
      else:
          parsed_result.append(line)
  return "\n".join(parsed_result)

```

