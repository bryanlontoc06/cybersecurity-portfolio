# 📌 Digital Forensics Fundamentals

## Introduction to Digital Forensics

Forensics is the application of methods and procedures to investigate and solve crimes. The branch of forensics that investigates cyber crimes is known as **digital forensics**. **Cyber crime** is any criminal activity conducted on or using a digital device. Several tools and techniques are used to investigate digital devices thoroughly after any crime to find and analyze evidence for necessary legal action.

## Digital Forensics Methodology

The digital forensics team has various cases requiring different tools and techniques. However, the National Institute of Standards and Technology (NIST) defines a general process for every case. The NIST works on defining frameworks for different areas of technology, including cyber security, where they introduce the process of digital forensics in four phases.

Arrow showing the four phases Collection, Examination, Analysis, and Reporting.

1. Collection: The first phase of digital forensics is data collection. Identifying all the devices from which the data can be collected is essential. Usually, an investigator can find personal computers, laptops, digital cameras, USBs, etc., on the crime scene. It is also necessary to ensure the original data is not tampered with while collecting the evidence and to maintain a proper document containing the collected items’ details. We will also be discussing the evidence-acquisition procedures in the upcoming tasks.

2. Examination: The collected data may overwhelm investigators due to its size. This data usually needs to be filtered, and the data of interest needs to be extracted. For example, as an investigator, you collected all the media files from a digital camera on the crime scene. You may only require some of the media as you are concerned with the media recorded on a specific date and time. So, in the examination phase, you would filter the media files of the required time and move them to the next phase. Similarly, you may only need the data of a specific user from a system containing numerous user accounts. The examination phase helps you filter this particular data for analysis.

3. Analysis: This is a critical phase. The investigators now have to analyze the data by correlating it with multiple pieces of evidence to draw conclusions. The analysis depends upon the case scenario and available data. The analysis aims to extract the activities relevant to the case chronologically.

4. Reporting: In the last phase of digital forensics, a detailed report is prepared. This report contains the investigation’s methodology and detailed findings from the collected evidence. It may also contain recommendations. This report is presented to law enforcement and executive management. It is important to include executive summaries as part of the report, considering the level of understanding of all the receiving parties.

📊 Summary Table of NIST Phases

|Phase| Main Goal| Analogy|
|--|--|--|
|**Collection**| Secure and preserve evidence| Sealing a crime scene and "bagging" physical evidence.|
|**Examination**| Filter and extract relevant data| Searching for specific fingerprints in a house full of items.|
|**Analysis**| Draw conclusions and build a timeline |Connecting the dots to determine how the crime happened.|
|**Reporting**| Present findings to stakeholders| Testifying in court or submitting a final investigation paper.|


The different fields of digital forensics around it.

**Computer forensics**: The most common type of digital forensics is computer forensics, which concerns investigating computers, the devices most commonly used in crimes.

**Mobile forensics**: Mobile forensics involves investigating mobile devices and extracting evidence such as call records, text messages, GPS locations, and more.

**Network forensics**: This area of forensics covers investigation beyond individual devices. It includes the whole network. The majority of the evidence found in networks is the network traffic logs.

**Database forensics**: Many critical data is stored in dedicated databases. Database forensics investigates any intrusion into these databases that results in data modification or exfiltration.

**Cloud forensics**: Cloud forensics is the type of forensics that involves investigating data stored on cloud infrastructure. This type of forensics sometimes gets tricky for the investigators as there is little evidence on cloud infrastructures.

**Email forensics**: Email, the most common communication method between professionals, has become an important part of digital forensics. Emails are investigated to determine whether they are part of phishing or fraudulent campaigns.


|TypeFocus| AreaKey| Evidence|
|--|--|--|
|Computer|Hard drives/Laptops|Files, OS logs, Browser history|
|Mobile| Smartphones/Tablets|SMS, Call logs, GPS, Apps|
|Network|Network Traffic|Logs, Packet captures, IP addresses|
|Database|SQL/NoSQL Databases|Modified records, Query logs|
|Cloud|AWS, Azure, GCP|Virtual logs, API calls|
|Email|Communication| Metadata, Headers, Phishing links|


## Evidence Acquisition

📌 Summary: Digital Evidence Acquisition

At this stage, the primary goal is to collect data securely, legally, and without alteration. There are three layers of "protection" that must be followed:

1. Legal Protection: Proper AuthorizationFinding the evidence isn't enough; you must have the legal right to take it.

**Importance**: Digital data is private and sensitive. If collected without proper authority (search warrant or corporate approval), it is deemed "inadmissible"—meaning it cannot be used as evidence in a legal proceeding.

**Rule**: Always secure written authorization before touching any device.


2. Process Protection: Chain of CustodyThis is the formal documentation that proves the evidence has not been switched, lost, or altered while in the investigators' possession.

**What it includes**: Name of the collector, date and time of collection, storage location, and a detailed log of every person who accessed or moved the device.


**Importance**: It provides Accountability. It proves to the court that the evidence presented is exactly the same as the evidence seized from the crime scene.

3. Technical Protection: Write BlockersThis is a technical safeguard used to ensure that the "original data" remains 100% unchanged during the imaging process.

**How it works**: When a suspect's hard drive is connected to a forensic workstation, the Write Blocker intercepts and stops any "write" signals (such as changing file timestamps or saving temporary system files).

**Importance**: It preserves Data Integrity. It ensures that the digital copy you are analyzing is a "bit-for-bit" perfect match of the original source.


💡 The "CSI" Comparison Table

|Tool/Process|Purpose|What happens if ignored?|
|--|--|--|
|Authorization|Legal Right|The evidence will be dismissed in court.|
|Chain of Custody|Evidence Tracking|The suspect can claim the evidence was "planted" or tampered with.|Write Blocker|Data Integrity|Metadata (like timestamps) will change, ruining the investigation's timeline.|