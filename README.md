![Integration tests](https://github.com/EsadCetiner/roundcube-rule-exclusions-plugin/actions/workflows/integration.yml/badge.svg)

# Roudcube-rule-exclusions-plugin
This plugin contains rule exclusions for Roundcube Webmail. These rules are designed to resolve common false positives and allow for easier intergration with OWASP CRS.

## Requirements
- CRS Version 4.0 or newer
- ModSecurity compatable Web Application Firewall

## Installation

For full and up to date instructions on installing plugins, please refer to [How to Install a Plugin](https://coreruleset.org/docs/concepts/plugins/#how-to-install-a-plugin) in the official CRS documentation.

### Conditionally enable plugins for multi-application environments

For full and up to date instructions on how to conditionally enable/disable this plugin on a multisite environment, please refer to [Conditionally enable plugins for multi-application environments](https://coreruleset.org/docs/concepts/plugins/#conditionally-enable-plugins-for-multi-application-environments) in the official CRS documentation.

### Configuration

Since Roundcube sends all requests to the same URL path (Depending on your HTTP server configuration), by default this plugin will unconditionally apply all rule exclusions to all URL paths. You can optionally configure what URL path this plugins will run under by uncommenting and modifying rule `9519001` in `roundcube-rule-exclusions-before.conf`

## Sieves filter false positive

If you use sieve filter sets, then you may still encounter some false positives. This is because the number within the brackets for parameter `_rule_target[0][]` constantly changes and it's not possible to exclude each and every target. If you do encounter a false positive then you'll have to fix it like so below:

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
    ver:'roundcube-rule-exclusions-plugin/1.0.3'"
```

Unfortunately, this is the only solution I'm aware of without completely disabling CRS. Please open an issue/PR if you think you've found a solution to this issue.

## Reporting false positives
If you find a false positive that this plugin does not cover then please open a new issue or pull request, if creating an issue then please include the following details:

1. CRS Version
2. ModSecurity/Coraza Version
3. modsec audit logs
4. what caused the false positive

Pull requests are welcomed if you know how to fix the issue, but please make sure to include tests if possible.

## License

Copyright (c) 2023-2025 Esad Cetiner

This plugin is distributed under GNU General Public License V2 (GPLv2), please see the included LICENSE file for details.
