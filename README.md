# Lab-Recognizing-Phishing-With-GenAI

## Background
Scientists at International Fusion Tech Power Corporation Limited recently reported suspicious emails to the security team, prompting a comprehensive analysis to unveil a potential phishing attack. The team’s scrutiny extends to certain scientists' systems, revealing a background program.
Further investigations uncovered a network log exposing suspicious activities across the organization. 

## Scope
The scope will include relevant logs, identifying the primary remote IP address, and determine associated port numbers for communication, meaning this report will primary focus on the network infrastructure. In addition, generative AI models such as ChatGPT will be used to assist in detecting anomalies and generating playbooks to prevent future incidents. 

## Findings
### Phishing Analysis
- The first step was to leverage generative AI in order to quickly assess if typical phishing traits were present or not. The email is presented here: 
> **Subject: Urgent Research Collaboration Opportunity - Exclusive Nuclear Insights!**
>
> Dear Dr. Simson,
>
> We hope this message finds you well. Our esteemed organization, the International Nuclear Fusion Advancements Consortium (INFAC), has recently come across ground breaking research that aligns perfectly with your expertise. We believe your involvement is crucial for the success of this venture.
>
> Attached to this email, you will find a PDF document containing detailed information on the collaborative project. Your swift review and valuable insights are of utmost importance to us.
>
> Download the PDF now: www.infac.com/proposal.pdf
>
> Your commitment to nuclear research has garnered widespread recognition, and we are confident that your expertise will play a pivotal role in advancing our shared goals.
>
> Please respond at your earliest convenience to confirm your participation. We eagerly await your esteemed contribution to this unprecedented initiative.
>
> Best Regards,
> Dr. Smith
> Director of Collaborative Research
> International Nuclear Fusion Advancements Consortium (INFAC)

- The recipient, Dr. Simson, had never heard of this organization although he is deeply involved in collaboration and research worldwide. This sounded suspicious so he contacted the security team to analyze. In order to leverage generative AI’s context expertise and save time, the following prompt was used by the security team:
> I am a security analyst working for a nuclear fusion company. The team recently reported to us that they received suspicious emails, and upon further investigation the team found a background program running as well as suspicious log activities across the organization.
>
> Here is the email: 'insert email here'
>
> I need you to take on the role of an expert in phishing and determine whether and/or how this email exhibits signs of a phishing attempt.
- chatGPT 4.0 mini then responded:
> Analyzing the email you provided, several signs indicate that it may be a phishing attempt. Here are the key points to consider:
>
> **1. Urgency and Pressure**
> • The subject line and the content of the email create a sense of urgency ("Urgent Research Collaboration Opportunity"). Phishing emails often use urgency to prompt quick action without careful consideration.
>
> **2. Unsolicited Contact**
> • If Dr. Simson did not expect to receive an email from the International Nuclear Fusion Advancements Consortium (INFAC) or has no prior relationship with them, this could be a red flag. Phishing attempts often come from unknown or unexpected sources.
>
> **3. Generic Greeting**
> • The email starts with "Dear Dr. Simson," which is somewhat formal but lacks personalization. Phishing emails often use generic greetings instead of addressing the recipient by name or referencing specific details about them.
>
> **4. Suspicious Links**
> • The email includes a link to download a PDF document. Hovering over the link (without clicking) may reveal that it does not lead to a legitimate or expected domain. Phishing emails often contain links that lead to malicious websites designed to steal information or install malware.
>
> **5. Attachment Warning**
> • The email mentions an attachment (a PDF document). Phishing attempts frequently include attachments that may contain malware or other harmful content. Legitimate organizations typically do not send unsolicited attachments.
>
> **6. Unusual Sender Information**
> • The sender's email address should be verified. If the email address does not match the official domain of the organization (e.g., INFAC), it could indicate a phishing attempt. Phishers often use email addresses that look similar to legitimate ones but have slight variations.
>
> **7. Lack of Specific Details**
> • The email lacks specific details about the research collaboration, such as project names, timelines, or other relevant information that would typically be included in a legitimate business proposal.
>
> **8. Pressure for Quick Response**
> • The request for a swift review and confirmation of participation is a tactic used to rush the recipient into making a decision without fully assessing the situation.
>
> **Conclusion**
>
> Based on these observations, the email exhibits several characteristics commonly associated with phishing attempts. It is advisable to treat this email with caution, avoid clicking any links or downloading attachments, and report it to your organization's IT or security team for further investigation. Additionally, verifying the legitimacy of the sender and the organization they claim to represent is crucial.


This is in line with personal analysis from the security team, which specifically highlighted lack of specific details in the project (to invite curiosity to click on the PDF) as well as driving a sense of urgency in the header. 

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
- the team then used the following prompt to analyze the Python code for malicious traits:
> Building on the previous context of this case, analyze this code and find any indications of malicious intent.
- chatGPT 4.0 mini provided these results:
> Analyzing the provided Python code, it appears to be designed for processing .docx files in a specified directory to identify certain keywords ("nuclear" and "research") and write the names of files containing those keywords to an output file. Here are some observations regarding potential indications of malicious intent:
>
> **1. Keyword Targeting**
> • The code specifically looks for the keywords "nuclear" and "research." While this could be legitimate in a research context, targeting specific keywords related to sensitive topics (like nuclear research) could indicate an intent to gather information on sensitive subjects.
>
> **2. File Handling**
> • The code appends the names of files containing the keywords to an output file (important.doc). This could be seen as a way to compile a list of documents that may contain sensitive or valuable information, which could be used for malicious purposes.
>
> **3. Lack of User Notification**
> • The script runs without any user interaction or notification about what it is doing. If this code were executed on a system without the user's knowledge, it could be considered malicious, as it silently collects information.
>
> **4. Potential for Abuse**
> • If deployed in an environment where it has access to sensitive documents, the code could be used to identify and extract information from documents that contain specific keywords, which could be exploited for espionage or data theft.
>
> **5. Output File Naming**
> • The output file is named important.doc, which could be misleading. It may suggest that it contains important information, but in reality, it is a list of files that match certain criteria. This could be a tactic to disguise the true nature of the file.
>
> **6. No Error Handling**
> • The code lacks error handling, which could lead to issues if it encounters files that cannot be read or if the output file cannot be created. While this is not directly indicative of malicious intent, it does suggest a lack of robustness that could be exploited in a more sophisticated attack.
>
> **Conclusion**
>
> While the code itself does not contain overtly malicious actions (like deleting files or sending data over the network), its purpose of identifying and compiling a list of documents based on specific keywords related to sensitive topics raises concerns. If used without proper authorization or in a context where it could infringe on privacy or security, it could be considered a tool for malicious intent.
>
> In a security context, it is crucial to ensure that such scripts are used ethically and with the consent of the data owners, especially when dealing with sensitive information.

It is always important to use critical thinking when using generative AI. It should not be taken completely at face value, but rather as an important tool to produce a quick overview that security professionals can review and draw their own conclusions from. Upon reviewing the output, the team is in agreement. Given the context of a nuclear program which has wide-ranging geopolitical implications, it makes sense that potential state/non-state actors may want access to very sensitive information. In this case, the malicious actor can gain access to sensitive files and exfiltrate them to the mentioned important.doc file. 

## Network log analysis
The team then moved to analyze the network log that identified suspicious activity across several computers. Generative AI was used to quickly analyze the log, pinpoint the remote IP address, and determine the relevant port numbers.
- The prompt used:
> Building on this situation, a log file detailing suspicious activity across several computers was found. Analyze the log, determine the remote IP where the maximum outbound traffic originated and determine the relevant port. 'log here'
- The output:
> **Summary of Outbound Connections**
>
> **1. Destination IP: 186.20.20.27**
> • Ports Used:
>   - 21 (TCP) - 3 occurrences
>   - 8080 (TCP) - 3 occurrences
>   - 5000 (UDP) - 3 occurrences
> • Source IPs:
>   - 101.123.171.5
>   - 101.123.171.15
>   - 101.123.171.3
>   - 101.123.171.18
>   - 101.123.171.6
>   - 101.123.171.16
>   - 101.123.171.9
>   - 101.123.171.17
>
> **2. Destination IP: 176.30.30.27**
> • Ports Used:
>   - 5000 (UDP) - 3 occurrences
>   - 8080 (TCP) - 4 occurrences
>   - 21 (TCP) - 3 occurrences
> • Source IPs:
>   - 101.123.171.8
>   - 101.123.171.20
>   - 101.123.171.12
>   - 101.123.171.7
>   - 101.123.171.19
>   - 101.123.171.11
>   - 101.123.171.2
>   - 101.123.171.13
>   - 101.123.171.4
>
> **Analysis of Outbound Traffic**
>
> • Destination IP with Maximum Outbound Traffic:
>   - 186.20.20.27 has 8 connections (3 on port 21, 3 on port 8080, and 2 on port 5000).
>
> • Relevant Ports:
>   - The ports used for outbound connections to 186.20.20.27 are:
>     - Port 21 (TCP): 3 occurrences
>     - Port 8080 (TCP): 3 occurrences
>     - Port 5000 (UDP): 2 occurrences
>
> **Conclusion**
>
> The remote IP where the maximum outbound traffic originated is 186.20.20.27, with the relevant ports being 21 (TCP) and 8080 (TCP), both of which had the highest number of connections. This indicates that there is significant suspicious activity directed towards this IP, which is noted as a known malicious IP in the log descriptions.

The generative AI was able to quickly identify the relevant information for the team, which allowed them to quickly generate this report with key findings.

## Playbook generation
The team then moved to generate a playbook for phishing and malware attacks, ensuring that these incidents can be properly handled in the future.
- The prompt:
> Given it is highly likely that the system at the organization is compromised through phishing, generate a playbook that will address phishing and malware attacks so they can be prevented and managed in the future. The playbook should cover but preventative and reactionary measures to make sure the most holistic approach is taken to handle incidents in the most efficient way possible.

- The response:
> **Phishing and Malware Attack Playbook**
>
> **Objective**
>
> To establish a comprehensive strategy for preventing, detecting, and responding to phishing and malware attacks within the organization.
>
> **1. Prevention**
>
> **1.1. Employee Training and Awareness**
> • Regular Training Sessions: Conduct mandatory training sessions on recognizing phishing attempts and safe email practices.
> • Simulated Phishing Attacks: Implement periodic simulated phishing exercises to test employee awareness and response.
> • Resource Distribution: Provide employees with resources (e.g., infographics, checklists) on identifying phishing emails and safe browsing practices.
>
> **1.2. Email Security Measures**
> • Email Filtering: Use advanced email filtering solutions to detect and block phishing emails before they reach users' inboxes.
> • Domain Authentication: Implement DMARC, DKIM, and SPF protocols to authenticate email senders and reduce spoofing.
> • Attachment and Link Scanning: Use security solutions that scan email attachments and links for malware before they are opened.
>
> **1.3. Endpoint Protection**
> • Antivirus and Anti-malware Software: Ensure all endpoints have up-to-date antivirus and anti-malware solutions installed.
> • Regular Software Updates: Implement a policy for regular updates of operating systems and applications to patch vulnerabilities.
> • Application Whitelisting: Restrict the execution of unauthorized applications to prevent malware from running.
>
> **2. Detection**
>
> **2.1. Monitoring and Logging**
> • Network Traffic Monitoring: Implement tools to monitor network traffic for unusual patterns indicative of phishing or malware activity.
> • Log Analysis: Regularly analyze logs from email servers, firewalls, and endpoint protection solutions for signs of suspicious activity.
>
> **2.2. Threat Intelligence**
> • Threat Intelligence Feeds: Subscribe to threat intelligence services to stay informed about the latest phishing tactics and malware signatures.
> • Incident Reporting: Establish a clear process for employees to report suspected phishing attempts or malware infections.
>
> **3. Response**
>
> **3.1. Incident Response Plan**
> • Immediate Isolation: If a phishing attempt is detected, isolate affected systems to prevent further compromise.
> • Investigation: Conduct a thorough investigation to determine the scope of the attack, including identifying affected users and systems.
> • Malware Removal: Use appropriate tools to remove any detected malware from compromised systems.
>
> **3.2. Communication**
> • Internal Notification: Inform relevant stakeholders (IT, management, affected users) about the incident and response actions taken.
> • External Communication: If necessary, prepare communication for external parties (customers, partners) regarding the incident and any potential impact.
>
> **3.3. Recovery**
> • System Restoration: Restore affected systems from clean backups if necessary, ensuring that all malware has been removed.
> • User Support: Provide support to affected users, including password resets and guidance on securing their accounts.
>
> **4. Post-Incident Review**
>
> **4.1. Lessons Learned**
> • Post-Mortem Analysis: Conduct a review of the incident to identify what went well and what could be improved in the response.
> • Update Policies and Procedures: Revise training, policies, and technical controls based on findings from the incident review.
>
> **4.2. Continuous Improvement**
> • Regular Drills: Conduct regular incident response drills to ensure readiness for future phishing and malware attacks.
> • Feedback Loop: Create a feedback mechanism for employees to share their experiences and suggestions for improving security practices.
>
> **5. Documentation and Reporting**
>
> • Maintain Records: Keep detailed records of all phishing attempts, malware incidents, and response actions taken.
> • Compliance Reporting: Ensure that all actions taken are documented for compliance with relevant regulations and standards.
>
> **Conclusion**
>
> This playbook serves as a guideline for preventing, detecting, and responding to phishing and malware attacks. Regular updates and training are essential to adapt to the evolving threat landscape and ensure the organization remains resilient against such attacks.

## Recommendations
Generative AI is a powerful tool that can leverage real-time alerts and anomaly detection to make teams more efficient and quicker at preventing threats. Generative AI should be integrated into systems such as SIEM. This will allow security teams to establish baseline behavior (such as in the case of the network anomalies mentioned previously) and receive real-time alerts of any anomalies found. 

In addition, generative AI can free up much more time to focus on more sophisticated threats that are not captured by traditional solutions by quickly analyzing issues such as phishing attempts or code. These sophisticated threats can be handled more efficiently because generative AI can compile data from various threat intelligence sources and leverage predictive analytics to stay steps ahead of future threats. 