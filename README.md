# Lab-Recognizing-Phishing-With-GenAI

## Background
Scientists at International Fusion Tech Power Corporation Limited recently reported suspicious emails to the security team, prompting a comprehensive analysis to unveil a potential phishing attack. The team’s scrutiny extends to certain scientists' systems, revealing a background program.
Further investigations uncovered a network log exposing suspicious activities across the organization.

## Scope
The scope will include relevant logs, identifying the primary remote IP address, and determine associated port numbers for communication, meaning this report will primary focus on the network infrastructure. In addition, generative AI models such as ChatGPT will be used to assist in detecting anomalies and generating playbooks to prevent future incidents. 

## Findings
### Phishing Analysis
- The first step was to leverage generative AI in order to quickly assess if typical phishing traits were present or not. The email is presented here: 
```
Subject: Urgent Research Collaboration Opportunity - Exclusive Nuclear Insights!
Dear Dr. Simson,
We hope this message finds you well. Our esteemed organization, the International Nuclear Fusion Advancements Consortium (INFAC), has recently come across ground breaking research that aligns perfectly with your expertise. We believe your involvement is crucial for the success of this venture.
Attached to this email, you will find a PDF document containing detailed information on the collaborative project. Your swift review and valuable insights are of utmost importance to us.
Download the PDF now: www.infac.com/proposal.pdf
Your commitment to nuclear research has garnered widespread recognition, and we are confident that your expertise will play a pivotal role in advancing our shared goals.
Please respond at your earliest convenience to confirm your participation. We eagerly await your esteemed contribution to this unprecedented initiative.
Best Regards,
Dr. Smith
Director of Collaborative Research
International Nuclear Fusion Advancements Consortium (INFAC)

```
- The recipient, Dr. Simson, had never heard of this organization although he is deeply involved in collaboration and research worldwide. This sounded suspicious so he contacted the security team to analyze. In order to leverage generative AI’s context expertise and save time, the following prompt was used by the security team:
```chatGPT
I am a security analyst working for a nuclear fusion company. The team recently reported to us that they received suspicious emails, and upon further investigation the team found a background program running as well as suspicious log activities across the organization. Here is the email: “<email above inserted here>”. I need you to take on the role of an expert in phishing and determine whether and/or how this email exhibits signs of a phishing attempt.
```
- chatGPT 4.0 mini then responded:
```chatGPT
Analyzing the email you provided, several signs indicate that it may be a phishing attempt. Here are the key points to consider:
1. Urgency and Pressure
    • The subject line and the content of the email create a sense of urgency ("Urgent Research Collaboration Opportunity"). Phishing emails often use urgency to prompt quick action without careful consideration.
2. Unsolicited Contact
    • If Dr. Simson did not expect to receive an email from the International Nuclear Fusion Advancements Consortium (INFAC) or has no prior relationship with them, this could be a red flag. Phishing attempts often come from unknown or unexpected sources.
3. Generic Greeting
    • The email starts with "Dear Dr. Simson," which is somewhat formal but lacks personalization. Phishing emails often use generic greetings instead of addressing the recipient by name or referencing specific details about them.
4. Suspicious Links
    • The email includes a link to download a PDF document. Hovering over the link (without clicking) may reveal that it does not lead to a legitimate or expected domain. Phishing emails often contain links that lead to malicious websites designed to steal information or install malware.
5. Attachment Warning
    • The email mentions an attachment (a PDF document). Phishing attempts frequently include attachments that may contain malware or other harmful content. Legitimate organizations typically do not send unsolicited attachments.
6. Unusual Sender Information
    • The sender's email address should be verified. If the email address does not match the official domain of the organization (e.g., INFAC), it could indicate a phishing attempt. Phishers often use email addresses that look similar to legitimate ones but have slight variations.
7. Lack of Specific Details
    • The email lacks specific details about the research collaboration, such as project names, timelines, or other relevant information that would typically be included in a legitimate business proposal.
8. Pressure for Quick Response
    • The request for a swift review and confirmation of participation is a tactic used to rush the recipient into making a decision without fully assessing the situation.
Conclusion
Based on these observations, the email exhibits several characteristics commonly associated with phishing attempts. It is advisable to treat this email with caution, avoid clicking any links or downloading attachments, and report it to your organization's IT or security team for further investigation. Additionally, verifying the legitimacy of the sender and the organization they claim to represent is crucial.
```
- This is in line with personal analysis from the security team, which specifically highlighted lack of specific details in the project (to invite curiosity to click on the PDF) as well as driving a sense of urgency in the header. 

## Malware Analysis
- The team was able to isolate the link to download the PDF and extract the file in a virtual sandboxed environment Using ChatGPT, the team used a crafted prompt to identify any malicious traits of the text. The code is as following:
```python
import os
from docx import Document

def identify_keywords_and_write(file_path, output_file):
    document = Document(file_path)
    has_keywords = any("nuclear" in paragraph.text.lower() and "research" in paragraph.text.lower() for paragraph in document.paragraphs)
    if has_keywords:
        with open(output_file, 'a') as output:
            output.write(os.path.basename(file_path) + '\n')

def process_directory(directory_path, output_file):
    for filename in os.listdir(directory_path):
        if filename.endswith(".docx"):
            file_path = os.path.join(directory_path, filename)
            identify_keywords_and_write(file_path, output_file)

if __name__ == "__main__":
    # Provide the path to the directory containing Word files
    word_files_directory = "path/to/your/word/files"
    # Output file for storing names of important files
    output_file_path = "important.doc"
    for file_name in os.listdir(word_files_directory):
        if file_name.endswith(".docx"):
            file_path = os.path.join(word_files_directory, file_name)
            identify_keywords_and_write(file_path, output_file_path)
    print("Process completed. Check 'important.doc' for results.")

```
