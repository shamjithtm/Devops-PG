Step 1: Configure AWS IAM Permissions  The bouncer needs permissions to create, update, and manage Rule Groups and IPSets within your AWS WAF configuration.  Create a custom IAM Policy with the following JSON template, and attach it to your IAM User or EC2 Instance Profile (recommended if running the bouncer on an EC2 instance):JSON{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "wafv2:UpdateWebACL",
                "wafv2:UpdateRuleGroup",
                "wafv2:UpdateIPSet",
                "wafv2:TagResource",
                "wafv2:GetWebACL",
                "wafv2:GetRuleGroup",
                "wafv2:GetIPSet",
                "wafv2:DeleteRuleGroup",
                "wafv2:DeleteIPSet",
                "wafv2:CreateRuleGroup",
                "wafv2:CreateIPSet"
            ],
            "Resource": "*"
        }
    ]
}


curl -s https://install.crowdsec.net | sudo sh


dnf install crowdsec-aws-waf-bouncer

sudo cscli bouncers add aws-waf-bouncer

Step 1: Open the configuration file
Bash
sudo nano /etc/crowdsec/bouncers/crowdsec-aws-waf-bouncer.yaml
Step 2: Replace the contents
Clear out the file and paste this clean, minimal template. Make sure to update the placeholder values (especially your API key, your actual AWS Web ACL name, and the regional scope):






YAML
api_key: <YOUR_API_KEY_HERE>
api_url: "http://127.0.0.1:8085/"
update_frequency: 10s

# This is the block the parser is complaining about:
waf_config:
  - web_acl_name: "your-web-acl-name"    # Replace with your actual AWS WAF ACL name
    fallback_action: "ban"
    rule_group_name: "crowdsec-rule-group"
    scope: "REGIONAL"                    # Use "CLOUDFRONT" if it's a CloudFront WAF
    region: "us-east-1"                  # Use the region where your WAF lives (e.g. us-east-1, us-west-2)
    ipset_prefix: "crowdsec-ipset"


    sudo /usr/bin/crowdsec-aws-waf-bouncer -c /etc/crowdsec/bouncers/crowdsec-aws-waf-bouncer.yaml -t























    
# Add the RPM repository setup script
curl -s https://install.crowdsec.net | sudo bash

# Install the CrowdSec security engine via DNF
sudo dnf install crowdsec -y

sudo dnf install crowdsec-firewall-bouncer-nftables -y

sudo nft list ruleset | grep crowdsec

sudo nft list table ip crowdsec

sudo cscli collections install crowdsecurity/nginx


ls -l /etc/crowdsec/acquis.d/

```
sudo vim /etc/crowdsec/acquis.d/nginx.yaml
```

Paste the following configuration into it to tell CrowdSec where your Nginx logs live:

YAML

```
filenames:
  - /var/log/nginx/*.log
labels:
  type: nginx
```


sudo cscli metrics

here it written 404 blocking 
sudo vim /etc/crowdsec/scenarios/http-probing.yaml

sudo systemctl restart crowdsec

sudo systemctl status crowdsec
sudo systemctl status crowdsec-firewall-bouncer



sudo cscli bouncers list

sudo nft list chain ip crowdsec crowdsec-chain-input

sudo cscli metrics

sudo tail -f /var/log/crowdsec.log


fake test 
sudo cscli decisions add --ip 1.1.1.1 --duration 5m --type ban

# Force the bouncer to completely rebuild the nftables schema
sudo systemctl stop crowdsec-firewall-bouncer
sudo systemctl start crowdsec-firewall-bouncer

lifetimeban
sudo cscli decisions add --ip 43.173.73.228 --duration 0 --type ban


nft firewall block

sudo nft add element ip crowdsec crowdsec-blacklists-cscli { 43.173.73.228 }


sudo dnf remove crowdsec-firewall-bouncer-nftables -y


sudo dnf install wget gettext -y


# 1. Download the latest release package
wget https://github.com/crowdsecurity/cs-nginx-bouncer/releases/latest/download/crowdsec-nginx-bouncer.tgz

# 2. Extract the archive
tar -xvzf crowdsec-nginx-bouncer.tgz

# 3. Enter the directory (The version folder name updates automatically)
cd crowdsec-nginx-bouncer-v*/

# 4. Run the installation script
sudo ./install.sh


sudo dnf install crowdsec-firewall-bouncer-nftables -y



Note this not working because appplicatjon loadbalamcer


Integrate with AWS WAF 





{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "wafv2:GetWebACL",
                "wafv2:UpdateWebACL",
                "wafv2:GetRuleGroup",
                "wafv2:UpdateRuleGroup",
                "wafv2:CreateIPSet",
                "wafv2:GetIPSet",
                "wafv2:UpdateIPSet"
            ],
            "Resource": "*"
        }
    ]
}


add this rule  to instane 


sudo dnf install crowdsec-aws-waf-bouncer -y


API key for 'my-aws-waf-bouncer':

   D6tu1vT0VTYMjiTiuqfmYuiAb9cP5P+U4j2IdY2Mzz8

Please keep this key since you will not be able to retrieve it!


You are looking at the foundational rule types inside the **AWS WAF Console**.

To link your CrowdSec automation to this panel using the `ip_set_only: true` solution we chose, you need to select **Custom rule**.

Here is exactly how to map it out in the AWS Console right now:

### Step 1: Create the IP Set First

Before making the rule, AWS WAF needs a container to hold the banned IPs.

1. In the left sidebar of the AWS WAF Console, click **IP sets**, then click **Create IP set**.
    
2. **Name:** Enter exactly `crowdsec-ipset`
    
3. **Region:** Choose `US West (N. California)` (since your config uses `us-west-1`).
    
4. **IP address version:** Choose **IPv4**.
    
5. Leave the IP box completely blank and click **Create**.
    

### Step 2: Create the Custom Rule in your Web ACL

Now, go to your **Web ACLs**, click on your specific Web ACL name, navigate to the **Rules** tab, click **Add rules**, and choose **Add my own rules and rule groups**.

Select **Custom rule** and configure these settings exactly:

- **Rule type:** Regular rule
    
- **Name:** `CrowdSec-WAF-Block`
    
- **If a request:** `matches the statement`
    
- **Inspect:** `Originates from an IP address in an IP set`
    
- **IP set:** Select the `crowdsec-ipset` you just created.
    
- **IP address to use:** Change this from _Source IP_ to **`IP address in header`** (This is the critical step for your Load Balancer setup).
    
- **Header name:** Enter `X-Forwarded-For`
    
- **Position if header has multiple IPs:** Choose `Last IP`.
    
- **Action:** Select **Block**.





sudo cat << 'EOF' > /etc/crowdsec/bouncers/crowdsec-aws-waf-bouncer.yaml
api_key: "D6tu1vT0VTYMjiTiuqfmYuiAb9cP5P+U4j2IdY2Mzz8"
api_url: "http://127.0.0.1:8080/"
update_frequency: 10s
daemon: true
log_media: file
log_dir: /var/log/
log_level: info

waf_config:
  - web_acl_name: "GeoIP-Block"
    fallback_action: ban
    scope: "REGIONAL"
    region: "us-west-1"
    ipset_prefix: "crowdsec-ipset"
    ip_set_only: true
    ip_header: "X-Forwarded-For"
    ip_header_position: "LAST"
EOF



# 1. Clear out systemd crash memory
sudo systemctl reset-failed crowdsec-aws-waf-bouncer

# 2. Fire up the bouncer daemon
sudo systemctl start crowdsec-aws-waf-bouncer

sudo cscli decisions delete --ip 54.177.56.156



# Check the IPv4 Cloud Set
aws wafv2 get-ip-set --name crowdsec-ipset --scope REGIONAL --region us-west-1 --id $(aws wafv2 list-ip-sets --scope REGIONAL --region us-west-1 | jq -r '.IPSets[] | select(.Name=="crowdsec-ipset") | .Id') | jq '.IPSet.Addresses'

# Check the IPv6 Cloud Set
aws wafv2 get-ip-set --name crowdsec-ipset-ipv6 --scope REGIONAL --region us-west-1 --id $(aws wafv2 list-ip-sets --scope REGIONAL --region us-west-1 | jq -r '.IPSets[] | select(.Name=="crowdsec-ipset-ipv6") | .Id') | jq '.IPSet.Addresses'





sudo journalctl -xeu crowdsec-aws-waf-bouncer.service --no-pager | tail -n 20



Step 1: Open the configuration file
Bash
sudo nano /etc/crowdsec/bouncers/crowdsec-aws-waf-bouncer.yaml
Step 2: Replace the contents with this complete block
Copy and paste this exact layout, which includes the missing rule_group_name line:

YAML
api_key: "euaAVAXRFrFYIsipD4Wtx67NHMF6SF4PNXvoV0N4HmU"
api_url: "http://127.0.0.1:8085/"
update_frequency: 10s
daemon: true
log_media: file
log_dir: /var/log/
log_level: info

waf_config:
  - web_acl_name: "GeoIP-Block"
    fallback_action: ban
    rule_group_name: "crowdsec-rule-group"  # <-- Added this mandatory field
    scope: "REGIONAL"
    region: "us-west-1"
    ipset_prefix: "crowdsec-ipset"
    ip_header: "X-Forwarded-For"
    ip_header_position: "LAST"
Step 3: Run the configuration test
Save and close the file, then verify it handles the change perfectly:

Bash
sudo /usr/bin/crowdsec-aws-waf-bouncer -c /etc/crowdsec/bouncers/crowdsec-aws-waf-bouncer.yaml -t
(You should get no output, which means validation passed).

Step 4: Restart the bouncer
Now fire up the system daemon:

Bash
sudo systemctl start crowdsec-aws-waf-bouncer
Check the status to ensure it transitions cleanly into that Polling decisions loop we saw working earlier:

Bash
sudo systemctl status crowdsec-aws-waf-bouncer






sudo cscli collections install crowdsecurity/nginx
sudo cscli collections install crowdsecurity/http-cve






















To achieve this, we will use the power of the setup you just built: Nginx will record the 404 errors, CrowdSec will read those logs and trigger a custom ban scenario, and your AWS WAF Bouncer will instantly push the ban to the AWS edge to block the attacker before they can hit your Laravel app again.

Here is the step-by-step guide to setting up the standard Nginx/Laravel protection rules along with your custom 10x 404 error blocking requirement.

Step 1: Install the Nginx & Base Web Collections
CrowdSec features pre-built bundles (collections) that instantly protect Laravel/Nginx environments from common web attacks, scanner bots, and .env file probing.

Run the following command to install the base Nginx and generic HTTP attack collections:

Bash
sudo cscli collections install crowdsecurity/nginx
sudo cscli collections install crowdsecurity/http-cve
Step 2: Create the Custom 10x 404 Blocking Scenario
By default, CrowdSec doesn't aggressively block 404s because standard users occasionally click broken links. However, we can create a custom scenario to catch aggressive scanners.

Create a new file for your custom rule:

Bash
sudo nano /etc/crowdsec/scenarios/nginx-404-bf.yaml
Paste the following YAML configuration exactly:

YAML
type: leaky_bucket
name: local/nginx-404-bf
description: "Block IPs triggering more than 10 404 errors"
# Filter specifically for Nginx access logs containing a 404 status code
filter: "evt.Meta.log_type == 'http_access-log' && evt.Parsed.status == '404'"
groupby: "evt.Meta.source_ip"
capacity: 10          # Number of 404s allowed before triggering the block
leakspeed: 30s        # The bucket empties 1 count every 30 seconds
duration: 4h          # How long the malicious IP will be banned (e.g., 4 hours)
labels:
  service: http
  type: scan
  remediation: true
Save and close the file (Ctrl+O, Enter, Ctrl+X).

Step 3: Tell CrowdSec Where Your Nginx Logs Live
CrowdSec needs to monitor your Nginx access logs to catch these events. Open your data acquisition configuration file:

Bash
sudo nano /etc/crowdsec/acquis.yaml
Add the following block to the bottom of the file (adjust the filenames paths if your Laravel site writes to a custom log file location):

YAML
filenames:
  - /var/log/nginx/access.log
  - /var/log/nginx/*access.log
labels:
  type: nginx
---
filenames:
  - /var/log/nginx/error.log
  - /var/log/nginx/*error.log
labels:
  type: nginx
Save and close the file.

Step 4: Configure Nginx to Pass Real IPs (Crucial for AWS WAF)
Because your application is behind AWS WAF, Nginx might see AWS load balancer IP addresses instead of the attacker's actual IP address. We must ensure Nginx extracts the real client IP from the X-Forwarded-For header.

Open your primary Nginx configuration file:

Bash
sudo nano /etc/nginx/nginx.conf
Inside the http { ... } block, ensure these lines are present:

Nginx
real_ip_header X-Forwarded-For;
set_real_ip_from 0.0.0.0/0; # In production, restrict this to your specific AWS VPC / ALB CIDR range
Test your Nginx syntax and reload the service:

Bash
sudo nginx -t
sudo systemctl reload nginx
Step 5: Restart CrowdSec and Test
Restart the core CrowdSec security engine to load the new data acquisition paths and your custom 404 brute-force rule:

Bash
sudo systemctl restart crowdsec
How to test your new rule:
From a separate machine or external terminal, intentionally trigger 11 rapid 404 requests against your Laravel application:

Bash
for i in {1..11}; do curl -I https://yourdomain.com/non-existent-page-${i}; done
Now, check the active alerts on your staging server:

Bash
sudo cscli decisions list
You will see the target external IP blocked under the local/nginx-404-bf rule. Within 10 seconds, your AWS WAF bouncer will automatically inherit this decision and block the IP at the AWS infrastructure edge!
