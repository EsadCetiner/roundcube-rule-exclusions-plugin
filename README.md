# Roudcube-rule-exclusions-plugin
This plugin contains rule exclusions to fix false positives when using Roundcube Webmail with the OWASP CoreRuleSet.

# Requirements
- CRS Version 4.0 or newer
- ModSecurity compatable Web Application Firewall

# How to install the plugin

Copy and paste the file ``roundcube-rule-exclusions-before.conf`` and ``roundcube-rule-exclusions-config.conf`` file into your CRS plugins folder.

create a include for the file ``plugins/roundcube-rule-exclusions-config.conf`` and ``plugins/roundcube-rule-exclusions-before.conf`` after ``crs-setup.conf`` but before loading any CRS rules.

See below for an example on how to create the includes:
```
Include /path/to/coreruleset/modsecurity.conf
Include /path/to/coreruleset/crs-setup.conf

Include /path/to/coreruleset/plugins/roundcube-rule-exclusions-config.conf
Include /path/to/coreruleset/plugins/roundcube-rule-exclusions-before.conf

Include /path/to/coreruleset/rules/*.conf
```

Then reload your WAF to apply the new changes (Restart for Nginx ModSec users)

You can also refer to official CRS documentation on how to install a plugin https://coreruleset.org/docs/concepts/plugins/#how-to-install-a-plugin

# Disabling the plugin
The plugin can be disabled by uncommenting rule 9519000 inside ``plugins/roundcube-rule-exclusions-config.conf`` or by removing the includes for this plugin.

# Reporting false positives
If you find a false positive that this plugin does not cover then please open a new issue or pull request, if creating an issue then please include the following details:

1. CRS Version
2. ModSecurity/Coraza Version
3. modsec audit logs
4. what caused the false positive