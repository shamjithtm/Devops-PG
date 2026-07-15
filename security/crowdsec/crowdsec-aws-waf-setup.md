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
