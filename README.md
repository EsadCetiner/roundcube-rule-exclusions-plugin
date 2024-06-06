![Integration tests](https://github.com/EsadCetiner/roundcube-rule-exclusions-plugin/actions/workflows/integration.yml/badge.svg)

# Roudcube-rule-exclusions-plugin
This plugin contains rule exclusions to fix false positives when using Roundcube Webmail with the OWASP Core Rule Set.

## Requirements
- CRS Version 4.0 or newer
- ModSecurity compatable Web Application Firewall

## Installation

For full and up to date instructions on installing plugins, please refer to [How to Install a Plugin](https://coreruleset.org/docs/concepts/plugins/#how-to-install-a-plugin) in the official CRS documentation.

### Configuration

Since Roundcube sends all requests to the same URL path, by default this plugin will unconditionally apply all rule exclusions to all URL paths. This behavior is configurable, you can choose to only apply rule exclusions to a specific URL path that Roundcube is installed on (For example, /mail/ or /roundcube/) by editing the rule ``9519001`` in ``plugins/roundcube-rule-exclusions-config.conf``. This is useful if you are hosting multiple web applications on the same domain and you only want to enable the Roundcube plugin on Roundcube.

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

Pull requests are welcomed if you know how to fix the issue, but please make sure to include tests if possible.

## License

Copyright (c) 2023-2024 Esad Cetiner

This plugin is distributed under GNU General Public License V2 (GPLv2), please see the included LICENSE file for details.
