## About the Lab
This is the first home lab in which we will be creating a VM, opening it up for attackers to exploit, collect logs & make a Workbook which shows where people are attacking/trying to attack from 

## Resources
The inspiration for this lab is coming from Josh Madakor.

> *Cyber Home Lab from ZERO and Catch Attackers! Free, Easy, and REAL (Microsoft Sentinel 2025) - YouTube*

 ## AIM
 <img width="848" height="478" alt="image" src="https://github.com/user-attachments/assets/f178f069-a252-4a1d-9977-97b13d31eb8b" />


The aim of the lab is very simple. 
1. Create a workspace
2. Set up a VM in Azure
3. Make it vulnerable intentionally: Allow inbound traffic
   3.1. We will enable inbound traffic from firewall & open all ports on the Network Security Group(NSG)
4. Give some time for attackers to attack our vulnerable VM
5. Collect the logs in the log analytics workspace
6. Forward those logs in MS Sentinel, the SIEM solution provided by Azure
7. Make an attack map out of the logs in the workbook

The following 2 are not part of the video he created, so I will add them now or maybe later, before my free trial runs out of credit

9. (optional/Additional) Create Alerts & Incidents
10. (optional/Additional) Automatically close incidents

**Create a workspace in Azure
Well I have been playing around with azure so the 30 days free tier is already enabled, so can't documetn that part here. So we will be starting with setting up Workspace

portal.azure.com > Searchbar > Resource Group

<img width="1918" height="383" alt="image" src="https://github.com/user-attachments/assets/9f53fcf9-956a-4f07-9474-f0c107027dd1" />

Here we don't have any RG beforehand

>*Well i deleted all the one i was playing around earlier for the simplicity of the lab*

Hit +Create

<img width="1442" height="664" alt="image" src="https://github.com/user-attachments/assets/a74c2a96-65cb-48c0-816d-04ad19ce2880" />

Choose the Subscription, 

Enter the Resource Group Name, 
>*This is important as while learning I had so many RG & got confused & wasted a lot of time*

Choose the Region, *I am in Canada so I will choose the nearest location for the least credit Utilization*

Hit Next

<img width="775" height="906" alt="image" src="https://github.com/user-attachments/assets/24c9aabe-7aff-4fe3-8e1c-f3f48ff3334c" />


At this moment, we are not playing around with tags, so just hit next & Create

<img width="749" height="814" alt="image" src="https://github.com/user-attachments/assets/8c86b33a-15af-419f-9d0e-f068be634194" />

<img width="1920" height="350" alt="image" src="https://github.com/user-attachments/assets/48d82b71-5fa8-496f-84fb-dc241f0fda73" />

Upon refreshing we can see the our RG is up & running

<img width="923" height="289" alt="image" src="https://github.com/user-attachments/assets/48db6281-fd28-495e-834c-e137fc4258eb" />


## Create A VM & make it Vulnerable
Again in searchbar, 

Look for Virtual Machine

<img width="561" height="290" alt="image" src="https://github.com/user-attachments/assets/a3ff66e1-4261-4179-9a3e-ca2ec713f8bb" />


Again, we don't have any VM here, so we will create one
<img width="639" height="640" alt="image" src="https://github.com/user-attachments/assets/f30ad9f1-d60c-4bce-835c-4b6bf58adb82" />

>Now beign the messy part, i struggled here for hrs before understanding what mistakes i was making

1. Choosing the Same Subscription name, RG. 
2. Give name to VM
3. Provide Region
4. Security type: Standard
5. Image: Windows 11 Pro, Version 25H2(Preview) - x64 Gen2
<img width="877" height="839" alt="image" src="https://github.com/user-attachments/assets/6a9c247a-b898-4c64-86b3-b19428e02d8c" />

-----------

>*in case you are unable to find the exact image, hit see all image*

>marketplace will open up, search in the Marketplace seachbar windows 11 preview, under select we will be able to find our required image

<img width="1113" height="618" alt="image" src="https://github.com/user-attachments/assets/025edf0f-8e17-485a-9676-c08482bf5315" />

-----------
6. Next, choose the size, like RAM, CPUs, etc
7. Provide the username & password
8. Confirm the licensing & hit Disks
9. We will continue with our budget stuff,
10.   OS Disk type: Standard HDD
11.   Delete with VM (this will disallocate all the resources once we del our VM)
12.   Hit Networking
<img width="814" height="772" alt="image" src="https://github.com/user-attachments/assets/9c7abf22-3847-4d5d-a1c1-e052c45449ff" />

13. Nothing special with Networking & Management
14. Monitoring: Diagnostics -> Disable
15. Once it says validation passed, just hit Create
<img width="769" height="815" alt="image" src="https://github.com/user-attachments/assets/290906c7-1c56-45b2-915c-574065d3c58b" />


Wait for the deployment till green sign shows up, 
<img width="1369" height="539" alt="image" src="https://github.com/user-attachments/assets/a27ae207-fcba-4803-ae8d-932e53636818" />

Next, go to its resources, 

In the Overview, we are interested in the Public IP of the VM
<img width="1912" height="539" alt="image" src="https://github.com/user-attachments/assets/de48c6dc-f4f9-490e-b2ff-396ae9a43127" />

As proof of concept that the VM is secure out of the box, we will try to ping it through 2 devices & both will fail to ping it. 
1. My own PC
<img width="620" height="309" alt="image" src="https://github.com/user-attachments/assets/d792a29c-18bd-42c1-98c9-d11edb8d2c35" />

2. Kali VM
<img width="883" height="335" alt="image" src="https://github.com/user-attachments/assets/d56664b8-4b20-40c3-ac64-36d9d6437d6e" />


### Make TOR01 vulnerable
Choose the VM from Virtual Machine & go to networking > Network Settings

We will be able to see that only the inbound rule is on RDP
<img width="1919" height="905" alt="image" src="https://github.com/user-attachments/assets/3f2cb064-d824-4176-b56c-8c813ecb9485" />

We don't want that & want people to attack it
So, 
1. Delete the RDP inbound port rule
<img width="894" height="179" alt="image" src="https://github.com/user-attachments/assets/8f7bd578-e934-49eb-8d0a-25b04f9b5e23" />

2. Create Port Rule > inbound port rule
<img width="1615" height="388" alt="image" src="https://github.com/user-attachments/assets/f8ba8b3d-aae1-4a90-a2ed-8d1ab8327640" />

3. Keep the destination port range to * > Hit Add
<img width="581" height="905" alt="image" src="https://github.com/user-attachments/assets/043661c4-f51f-43b5-84bb-f778ae7716de" />

Once done, we will be able to see the new port rule
<img width="1606" height="444" alt="image" src="https://github.com/user-attachments/assets/47597556-7499-46c1-8509-4ef723a90dc2" />

>Here again, if we try to ping, we won't be able to ping the device, so we have to make the firewall rule vulnerable too.

Let's connect to the VM using RDP, using the credentials we set up 
<img width="1523" height="532" alt="image" src="https://github.com/user-attachments/assets/0c79fbb5-f718-4bad-b85a-4a572b82bd7c" />
<img width="1019" height="688" alt="image" src="https://github.com/user-attachments/assets/c0891d9f-acb7-4398-a3ee-a63592ceee44" />

Make RDP connection & enter the credentials
<img width="690" height="811" alt="image" src="https://github.com/user-attachments/assets/c3d9dd07-43c9-4dec-95aa-b569d19e2e77" />

### Firewall Configuration in VM
Now in the VM, 
1. start > firewall
<img width="786" height="786" alt="image" src="https://github.com/user-attachments/assets/cad0ca88-c33b-4ca9-b3c7-41318f71bba4" />

2. Advanced settings > inbound rules > file & printer sharing > Enable Rule
<img width="1128" height="637" alt="image" src="https://github.com/user-attachments/assets/48a3584c-19fc-4bba-a423-d337c68001bf" />
<img width="1048" height="788" alt="image" src="https://github.com/user-attachments/assets/819a7d78-1b5f-4b01-a164-e863b91d4ea5" />

3. Go back to Windows Defender Firewall > Windows Defender Firewall Properties & turn all the **Firewall State**: off
<img width="1044" height="785" alt="image" src="https://github.com/user-attachments/assets/7dc0c5f9-a5ca-4acb-ac3d-a3c21a15e421" />

<img width="402" height="460" alt="image" src="https://github.com/user-attachments/assets/9df82f98-c5ad-46d7-a5ae-bef0eda52d33" />
<img width="400" height="199" alt="image" src="https://github.com/user-attachments/assets/8991cf6d-ed43-4d0d-a39f-30d47d09d87a" />
<img width="399" height="462" alt="image" src="https://github.com/user-attachments/assets/f655dfcf-7520-476d-bca5-f6d3dde6d393" />
<img width="1049" height="787" alt="image" src="https://github.com/user-attachments/assets/cb642cf5-0a00-4c5c-b094-3be76187ef75" />




>Now if we try to ping from our 2 machines now ping will be successful
<img width="726" height="299" alt="image" src="https://github.com/user-attachments/assets/35ff2b2b-c0ea-431e-b512-12f11c732a91" />
<img width="586" height="296" alt="image" src="https://github.com/user-attachments/assets/869cdb6b-4d28-4320-9234-43eef674390c" />


### Now we will let our VM be for some time so attackers can play around with it for some time. 
In the meantime, we will set up our log analytics workspace

<img width="1219" height="642" alt="image" src="https://github.com/user-attachments/assets/5b355ba0-70a3-4867-925a-70eb857fc750" />

Again, we will be using the same resource group & give a name for our log analytics workspace
<img width="958" height="190" alt="image" src="https://github.com/user-attachments/assets/e23afe4f-7c5f-4a75-a751-af1eabd9003b" />
<img width="742" height="908" alt="image" src="https://github.com/user-attachments/assets/738abc40-3525-464d-bfb3-0f3bba51e7fc" />

## Time For all Mighty _Sentinel_
### Ingest logs from our VM to Log Analytics Workspace
<img width="539" height="684" alt="image" src="https://github.com/user-attachments/assets/78dd157f-f27d-4633-9f8f-303bb25410d3" />
<img width="902" height="188" alt="image" src="https://github.com/user-attachments/assets/99455f64-41ef-4b0d-8f73-1357c9985c51" />
<img width="1691" height="890" alt="image" src="https://github.com/user-attachments/assets/76cda9a4-8d65-4582-bbcd-d29bc8f97fec" />
<img width="1917" height="290" alt="image" src="https://github.com/user-attachments/assets/be101c54-5364-463c-b3fa-273463f28832" />

>After it waits for some time so that Log Analytics can integrate with Sentinel
>
##_Time to move to MS Defender Portal_
Go to **security.microsoft.com**

MS Sentinel > Content Management > Content Hub > search for Windows Security Event > install
<img width="1913" height="988" alt="image" src="https://github.com/user-attachments/assets/24cf1b83-2820-4f7b-adc3-0c0851273919" />

once installed successfully, hit Manage > Select AMA > Manage Connector
<img width="1917" height="891" alt="image" src="https://github.com/user-attachments/assets/62fa5744-f6a3-4695-825f-ee036b36bd14" />

<img width="1078" height="896" alt="image" src="https://github.com/user-attachments/assets/f50c59bc-9aff-4a2d-9431-b9c201956997" />

This will tell the name of the table we will be able to see the logs: **_SecurityEvent_**

<img width="1008" height="893" alt="image" src="https://github.com/user-attachments/assets/72fd5b06-9bcb-4995-866d-d29d4625c8a9" />
<img width="646" height="897" alt="image" src="https://github.com/user-attachments/assets/723f879b-0bc2-41b7-8950-12b871998e4c" />
<img width="626" height="755" alt="image" src="https://github.com/user-attachments/assets/1a3e1e83-8bc4-40df-bbc8-5915cb7e9ce7" />
<img width="645" height="896" alt="image" src="https://github.com/user-attachments/assets/b601d449-68bc-4d5a-b300-9ede9b32d58c" />

Once created, we will be able to see the Rule created under AMA agent
<img width="1538" height="848" alt="image" src="https://github.com/user-attachments/assets/560e089f-8f59-40a7-8767-4eb738f15af9" />

To confirm that AMA has been configured to our TOR01 VM, we can visit the VM page > extensions & applications
<img width="897" height="536" alt="image" src="https://github.com/user-attachments/assets/4d042de3-3241-4754-84cf-f9b468186e77" />

To confirm our configuration is working as expected, 

Go to Log Analytics Workspace > Select the workspace > Logs > Search for SecurityEvent

If data shows up, this means our agent is able to take logs from our VM & now we can take advantage of the log analytics workspace
<img width="1919" height="946" alt="image" src="https://github.com/user-attachments/assets/da6aba00-52df-4a81-9b81-44207ba7bc17" />

>Interestingly, the VM was open to the internet for 2-3 hrs by now & there are 35K+ events,
>I was worried that I would have to manually create logs, but I didn't expect this much noise
<img width="1564" height="478" alt="image" src="https://github.com/user-attachments/assets/a947362f-252b-4aab-8ca2-6c45b7d27a95" />


If we check one of the IPs that tried to attack our machine
<img width="769" height="745" alt="image" src="https://github.com/user-attachments/assets/da76ea32-8086-4bdc-b216-6e2e53144748" />

It's indeed found to be suspicious
<img width="1901" height="795" alt="image" src="https://github.com/user-attachments/assets/a9621561-e1bc-4ce4-9722-e3abee13222b" />


## Map the IP address to a geographical location

> In the video, Josh gave a link to download a huge Excel which links IP address to lat & longitude, which we can use to link the geographical location of the IP address

>I won't be adding that here, so if anyone is following with me, kindly visit his YouTube video & raise his YouTube views. I guess that's the least I can do.

1. Visit MS Defender at security.microsoft.com
2. MS sentinel > Watchlist > New

>Watchlist: it's a place where we can upload data/logs directly to our Log Analytics Workspace, so we can later search that data in our Sentinel using KQL. That's what we will do at least   
<img width="1920" height="998" alt="image" src="https://github.com/user-attachments/assets/b366dd50-7cc1-420b-acbc-d87dee87ecc5" />

4. The name will be important, as we will be using it in our KQL query
<img width="1456" height="890" alt="image" src="https://github.com/user-attachments/assets/9759d5e9-4f4d-49c5-9130-5262461a1574" />

5. Upload the .cve & SearchKey: Network > Hit Create
<img width="1725" height="894" alt="image" src="https://github.com/user-attachments/assets/ffd6cc02-5157-40b4-b585-e46e13717408" />

This will send us back in MS Defender Portal, & if we click on our watchlist, its status will show how much processing has already been done & the time needed
<img width="1920" height="992" alt="image" src="https://github.com/user-attachments/assets/81a5048c-cda6-4cf2-8602-335103f88cd6" />

Now, if we try to link both of our tables together, we get the content of both tables
<img width="1560" height="741" alt="image" src="https://github.com/user-attachments/assets/9567a3d6-91fe-41ce-aae0-aba614a8aeea" />


The command we will use to test if both tables are merging well will be
>I tried using Union, but it didn't help. I don't know why the following command is working, but it works for some reason. Damn, have to work even harder on KQL
```
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == '92.63.197.69'
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents
```
<img width="1564" height="694" alt="image" src="https://github.com/user-attachments/assets/92c35868-ad62-4f68-b622-25644a1f08b9" />

Time to generate a GeoMap

Again back to MS defender portal
1. security.microsoft.com
2. MS sentinel > Threat Managemet > workbooks > Add Workbook
<img width="1912" height="977" alt="image" src="https://github.com/user-attachments/assets/b4d0622b-3b07-464b-9be3-cc1243fd8dfb" />

3. Hit edit & del what is already in there
<img width="1541" height="741" alt="image" src="https://github.com/user-attachments/assets/6dc9a91c-4d21-4841-81d7-b79ab0989de9" />

<img width="478" height="519" alt="image" src="https://github.com/user-attachments/assets/15f4f2d3-739b-44fd-a80b-44a799cdf683" />

4. Go to Advanced Editor & delete what is already in there & paste the following JSON of the map & hit apply
```
{
	"type": 3,
	"content": {
	"version": "KqlItem/1.0",
	"query": "let GeoIPDB_FULL = _GetWatchlist(\"geoip\");\nlet WindowsEvents = SecurityEvent;\nWindowsEvents | where EventID == 4625\n| order by TimeGenerated desc\n| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)\n| summarize FailureCount = count() by IpAddress, latitude, longitude, cityname, countryname\n| project FailureCount, AttackerIp = IpAddress, latitude, longitude, city = cityname, country = countryname,\nfriendly_location = strcat(cityname, \" (\", countryname, \")\");",
	"size": 3,
	"timeContext": {
		"durationMs": 2592000000
	},
	"queryType": 0,
	"resourceType": "microsoft.operationalinsights/workspaces",
	"visualization": "map",
	"mapSettings": {
		"locInfo": "LatLong",
		"locInfoColumn": "countryname",
		"latitude": "latitude",
		"longitude": "longitude",
		"sizeSettings": "FailureCount",
		"sizeAggregation": "Sum",
		"opacity": 0.8,
		"labelSettings": "friendly_location",
		"legendMetric": "FailureCount",
		"legendAggregation": "Sum",
		"itemColorSettings": {
		"nodeColorField": "FailureCount",
		"colorAggregation": "Sum",
		"type": "heatmap",
		"heatmapPalette": "greenRed"
		}
	}
	},
	"name": "query - 0"
}

```
<img width="1425" height="673" alt="image" src="https://github.com/user-attachments/assets/0a9980b4-98b1-402d-a4c9-688fa9409c3f" />

5. Once we hit apply we will get our desired MAP
<img width="1595" height="559" alt="image" src="https://github.com/user-attachments/assets/6b24146c-0d89-4a44-b33c-b0d3e5d6c0e9" />


The final Map looks like
<img width="1591" height="621" alt="image" src="https://github.com/user-attachments/assets/b544a56e-6149-4216-9348-5b1271582dfb" />

That is where the LAB ends & we can say mission successful. 


