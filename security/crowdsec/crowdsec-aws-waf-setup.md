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

