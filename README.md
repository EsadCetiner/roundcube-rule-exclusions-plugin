# Roudcube-rule-exclusions-plugin
This plugin contains rule exclusions to fix false positives when using Roundcube Webmail with the OWASP Core Rule Set.

## Requirements
- CRS Version 4.0 or newer
- ModSecurity compatable Web Application Firewall

## How to install the plugin

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

## Disabling the plugin
The plugin can be disabled by uncommenting rule 9519000 inside ``plugins/roundcube-rule-exclusions-config.conf`` or by removing the includes for this plugin.

## Sieves filter false positive

If you use sieve filter sets, you may encounter false positives that aren't fully resolved by this plugin. The number within the brackets for parameter ARGS:_rule_target[0][] will change based upon what sieve filter set you are creating a rule for. If you do encounter a false positive then you'll have to fix it like so below:

```
SecRule REQUEST_FILENAME "@beginsWith %{tx.roundcube-rule-exclusions-path}" \
    "id:1,\
    phase:1,\
    pass,\
    t:none,\
    nolog,\
    ctl:ruleRemoveTargetByTag=OWASP_CRS;ARGS:_rule_target[6][],\
    ctl:ruleRemoveTargetByTag=OWASP_CRS;ARGS:_rule_target[7][],\
    ctl:ruleRemoveTargetByTag=OWASP_CRS;ARGS:_rule_target[8][],\
    ctl:ruleRemoveTargetByTag=OWASP_CRS;ARGS:_rule_target[9][],\
    ctl:ruleRemoveTargetByTag=OWASP_CRS;ARGS:_rule_target[10][],\
    ver:'roundcube-rule-exclusions-plugin/1.0.0'"
```

Unfortunately, this is the only solution I'm aware of without completely disabling the Core Rule Set. Please open an issue/PR if you think you've found a solution to this issue.

## Reporting false positives
If you find a false positive that this plugin does not cover then please open a new issue or pull request, if creating an issue then please include the following details:

1. CRS Version
2. ModSecurity/Coraza Version
3. modsec audit logs
4. what caused the false positive
