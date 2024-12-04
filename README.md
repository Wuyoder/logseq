- Goal
	- AWS EC2 host a Logseq server (just a app server, storage is in your local machine)
- pre
	- intro
		- Logseq is an open source note taking app (almost like Obsidian)
	- why
		- Logseq has prettier UI and plugin sources than Obsidian
		- Logseq has a more concise input format, but Obsidian has richer markdown support.
		- Logseq support git commit history view originally, no need plugin
		- Logseq hierarchy system is page title depend
		- Logseq and Obsidian both support tag feature and graph view
		- Logseq officals can't get any information owing to your have fully control of storage , not like Notion (can choose onedrive, google drive, iCloud or just local as storage)
		- Logseq's Whiteboards is prettier than Obsidian, although both use canvas
	- ref
		- docker https://github.com/logseq/logseq/blob/master/docs/docker-web-app-guide.md
		- ssl license https://github.com/FiloSottile/mkcert
	- plugin in use
		- Tabs
		- Journals Calendar
		- Copy Code
- Steps
	- launch an EC2 instance
	- security group inbound rule add HTTPS
	- follow ref:docker
		- ```
		  #install docker and pull image
		  sudo amazon-linux-extras install docker
		  sudo usermod -aG docker ec2-user
		  docker pull ghcr.io/logseq/logseq-webapp:latest
		  
		  #prepare ssl 
		  curl -JLO "https://dl.filippo.io/mkcert/latest?for=linux/amd64"
		  chmod +x mkcert-v*-linux-amd64
		  cp mkcert-v*-linux-amd64 /usr/local/bin/mkcert
		  vim ssl.conf
		  ---
		  server {
		      listen  443   ssl;
		      ssl_certificate /etc/nginx/certs/your_ec2_public_ip.pem;
		      ssl_certificate_key /etc/nginx/certs/your_ec2_public_ip-key.pem;
		      server_name  localhost;
		  
		      location / {
		          root   /usr/share/nginx/html;
		          index  index.html index.htm;
		      }
		  }
		  ---
		  # create ssl.key
		  mkcert your_ec2_public_ip-key
		  
		  #check ssl.conf, your_ec2_public_ip-key.pem, your_ec2_public_ip.pem in your `pwd`
		  docker run -d --rm -p 443:443 -v `pwd`:/etc/nginx/certs -v `pwd`/ssl.conf:/etc/nginx/conf.d/ssl.conf ghcr.io/logseq/logseq-webapp:latest
		  ```
	- open browser and go to https://your_ec2_public_ip
	- ssl let Loqseq app can use filesystem api to access your local storage
	- All of this is redundant. You can just install Logseq app or use docker in your machine
- Last thing
	- Fully controlling my data is really important to me, so I refuse to use Notion
	- Versioning control with git is cool
	- Remember to terminate your EC2 instance.
