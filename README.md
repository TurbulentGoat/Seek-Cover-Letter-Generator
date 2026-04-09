# Seek Cover Letter Generator  
A Tampermonkey userscript that generates concise, personalized cover letters for job applications on seek.com.au. It extracts job details, allows customization of skills and saves frequently used details for efficiency.
Features

<img width="707" height="540" alt="image" src="https://github.com/user-attachments/assets/6a2153e6-1b5e-42a0-9475-7f2577b4b749" />

Generates tailored cover letters with job title and company name pulled from Seek job listings.
Customizable fields for top skills and company-specific interest.
Saves skills and achievements locally to streamline multiple applications.
Copies the generated letter to your clipboard with one click.

<img width="374" height="861" alt="image" src="https://github.com/user-attachments/assets/721911e6-2b9e-46cc-8e8c-0c331aca974b" />

## Prerequisites  

A modern web browser (e.g., Chrome, Firefox, Edge).
Tampermonkey browser extension.

## Installation  

### Install Tampermonkey:  

Chrome: Visit the Chrome Web Store, click "Add to Chrome," and confirm.
Firefox: Go to Firefox Add-ons, click "Add to Firefox," and allow permissions.
Edge: Find Tampermonkey in the Microsoft Edge Add-ons store, click "Get," and add it.
For other browsers, check Tampermonkey’s official site.


### Install the Userscript:  

Download or copy the script from this repository (seek-cover-letter-generator.js).
Open Tampermonkey: Click the Tampermonkey icon in your browser toolbar and select "Dashboard."
Create a new script: Click the "+" button in the Tampermonkey dashboard.
Paste the script: Replace the default template with the contents of seek-cover-letter-generator.js.
Save: Click the save icon or press Ctrl+S.

Alternatively, click the "Raw" button on the script file in this GitHub repository and Tampermonkey should prompt you to install it.


## Usage  

### Navigate to a Job Listing:  

Visit a job listing on seek.com.au.
Ensure the page displays the job title and company name.


### Generate a Cover Letter:  

Click the pink "Generate Cover Letter" button (top-right corner).
A new window opens with a form to customize your cover letter.


### Customise the Letter:  

Top Skills: Enter 2-3 relevant skills (e.g., "problem-solving, teamwork").
Why This Company?: Add a reason you’re interested in the company (e.g., "I admire your innovation").
Click "Save Details" to store skills and achievements for future use, or "Load Saved Details" to retrieve them.
Click "Clear Saved Details" to reset saved data.


### Generate and Copy:  

Click "Generate Cover Letter" to create the letter.
Review the letter in the textarea.
Click "Copy & Close" to copy the letter to your clipboard and close the window.


### Apply:  

Paste the cover letter into the Seek application form or your preferred editor for further tweaks.



### Notes  

The script works only on Seek job listing pages with valid job title and company name elements.
If the button doesn’t appear or the script fails, ensure you’re on a job listing page and Tampermonkey is enabled.
Customize the generated letter further to match specific job requirements for best results.
Sometimes the job title and/or company name need manually correcting.

### License
MIT License. See LICENSE for details.
### Contributing
Feel free to fork this repository, submit issues, or create pull requests to suggest improvements.
### Acknowledgments
Built for job seekers to streamline applications on Seek.com.au. Inspired by the need for quick, tailored cover letters.
