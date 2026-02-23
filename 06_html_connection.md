## How to set up a local HTML connection to storm
You can use the x2go interface to look at the output, but this is often slow and inconvenient. Another good option is to
access the HTML files via an SSH tunnel.

### 1. On storm terminal
Navigate to the directory containing your `.html` files (e.g., in `derivatives` if you want to view fmriprep output). 
From there, set up your web connection with:
```
python3 -m http.server 8080
```
You can choose any available port number instead of `8080`.

### 2. On your local computer terminal
Set up the ssh connection in your local terminal:
```
ssh -L 8080:localhost:8080 USER@storm.herseninstituut.knaw.nl
```
Replace `USER` with your storm username.  

### 3. On your local computer browser
After the connection is established, open your web browser and go to:
```
http://localhost:8080
```
