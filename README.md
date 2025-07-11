# SIEMsational CTF
I completed this project as part of CodePath’s CYB102 cybersecurity course to apply Splunk skills in a Capture-the-Flag (CTF) challenge. The goal was to solve security-related questions by searching through logs and datasets in Splunk. After completing this project, I strengthened my ability to write SPL search queries, analyze log data, and investigate simulated cyber incidents using Splunk’s Search and Reporting tools. Although I did not provide a screenshot of every challenge, I took some screenshots of the ones I thought were pretty cool and interesting.

## Tools & Technologies Used
- Ubuntu 22.04 LTS VM
- Splunk Enterprise (SIEM platform)
- Netflix dataset
- PathCode Inc. simulated attack logs

## What This Project Does
- Uses Splunk to solve CTF-style challenges across two datasets: Netflix data and simulated malware investigation logs.
- Builds search queries to analyze user login attempts, file uploads, and malware indicators.
- Identifies attack patterns such as unauthorized logins and file uploads by filtering through log fields.
- Answers incident investigation questions like identifying the attacker’s IP, user agents, and file hashes.
- Practices cybersecurity log analysis in a competitive environment to build real-world SIEM skills.

## Screenshots
### Finding what usernames the malicious IP address tried to log in as, and what username was used to upload the malware (Challenge 12)
<img src="https://github.com/user-attachments/assets/0cd2e1fe-6c5f-4d58-b470-55a203db034b" width="600"/>

---
### Find what the User Agent String of the attacker was when the successfully uploaded a file (Challenge 13)
<img src="https://github.com/user-attachments/assets/fd0b9663-b66a-46e4-9987-b2084f82862c" width="600"/>

---

### Identifying the IP and username that sent the malicious file (Challenge 14-15)
- As you can see, the IP that sent "EvilScript.exe" is 192.168.1.10. We can see that the username "ABurke" uploaded this file.
<img src="https://github.com/user-attachments/assets/6000bb9f-fe86-4b53-9beb-eb9f15e9fdc0" width="600"/>

## Part 1 - Searching the Netflix Data. Use index=main source=Netflix
For each challenge, document both:
1. The challenge answer
2. The search command used to find the answer

---
**👥 Challenge 1:  How many TV shows on Netflix are in the Docuseries genre?**
1. 170
2. index=main host="Netflix" type="TV Show" listed_in="Docuseries"
| stats count
---
**👥 Challenge 2:  How many movies on Netflix have a rating of TV-PG?**
1. 1080
2. index=main host="Netflix" type="Movie" rating="TV-PG"
| stats count
---
**👥 Challenge 3: How many movies on Netflix were released in the year 2020?**
1. 1034
2. index=main host="Netflix" type="Movie" release_year="2020"
| stats count
---
**👥 Challenge 4: What is the longest duration by season on Netflix, and what is its TV rating?**
1. 17 seasons, TV-14 (not sure if needed, but the name is Grey’s Anatomy)
2. index=main host="Netflix" type="TV Show"
| rex field=duration "(?<seasons>\d+)" 
| sort - seasons
| head 1
---
**👥 Challenge 5: How many movies on Netflix are listed as action and are rated PG-13?**
1. 296
2. index=main host="Netflix" type="Movie" rating="PG-13" 
| search listed_in="*Action*"
| stats count
---
**👥 Challenge 6: How many movies and TV shows on Netflix have their country of origin as Turkey?**
1. 210
2. index=main host="Netflix" country="Turkey" 
| stats count
---
**👥 Challenge 7: Which release year had the most movies rated G? (Not TV-G)**
1. 2009
2. index=main host="Netflix" type="Movie" rating="G" 
| stats count by release_year
| sort - count
---
**👥 Challenge 8: What two TV-Y7 rated shows were released in 2019 and were added to Netflix on November 22, 2019?**
1. Trolls: The Beat Goes On! AND The Dragon Prince
2. index=main host="Netflix" type="TV Show" release_year="2019" date_added="November 22, 2019" rating="TV-Y7"
---
**👥 Challenge 9: Which year had the most movies from the United States?**
1. 2017
2. index=main host="Netflix" type="Movie" country="United States" 
| stats count by release_year
| sort - count
---
**👥 Challenge 10: What is the oldest TV show by Release Year on Netflix?**
1. Pioneers: First Women Filmmakers* (if needed: 1925)
2. index=main host="Netflix" type="TV show"
| sort release_year 
| table title release_year
---
## Part 2 - Investigating the Malware (2pts each)
For Part 2, we are investigating an attacker who got into our systems that happened at PathCode Inc. For these logs use index=pathcode
---
**👥 Challenge 11: What was the IP address that uploaded the malware (MD5 hash: 3AADBF7E527FC1A050E1C97FEA1CBA4D)**
1. 192.168.1.10
2. index="pathcode" "3AADBF7E527FC1A050E1C97FEA1CBA4D"
---
**👥 Challenge 12: What usernames did that IP address try to login to the system as? Which one did they upload a file as?**
1. ABurke, Admin, Pi. They uploaded the file as ABurke.
2. index=pathcode IP="192.168.1.10"
| table _time Username Filename "File Hash" Event
---
**👥 Challenge 13: What was the User Agent String of the attacker when they successfully uploaded a file?**
1. Opera/75.0.3969.218
2. index=pathcode IP="192.168.1.10"
| table *
---
**👥 Challenge 14: Did any other users also upload a file around that time? If so, who and what was their IP address?**
1. Yes, Jmann. His IP is 192.168.1.7
2. index=pathcode ("File Upload" OR "File Uploaded")
| table _time Username IP Filename "File Hash" Event
---
**👥 Challenge 15: Looking at the uploaded hashes, what were the files called that the two users uploaded? Which one seems like it was malicious?**
1. EvilScript.exe and proposal.pdf. The file that seemed malicious is: EvilScript.exe
2. index=pathcode ("File Upload" OR "File Uploaded")
| table _time Username IP Filename "File Hash" Event
