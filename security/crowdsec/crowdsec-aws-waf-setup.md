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

