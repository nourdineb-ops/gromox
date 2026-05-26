# Outlook Mac compatibility notes

## Important deployment steps

After build/install, verify:

- /usr/share/gromox/folder_names.txt
- /usr/sbin/gromox-mkprivate

The Outlook Mac fix depends on:
- proper localized special folders:
  - Archive
  - Historique des conversations
  - Cache des destinataires
- correct mailbox provisioning during mkprivate

Validation:

sqlite3 /var/lib/gromox/user/<domain>/<user>/exmdb/exchange.sqlite3 "
select
  f.folder_id,
  f.parent_id,
  dn.propval as display_name,
  cc.propval as container_class
from folders f
left join folder_properties dn
  on dn.folder_id=f.folder_id and dn.proptag=805371935
left join folder_properties cc
  on cc.folder_id=f.folder_id and cc.proptag=907214879
order by f.folder_id;
"

Expected folders:
- Archive
- Historique des conversations
- Cache des destinataires

If folders appear as FLG-ERR-2:
- folder_names.txt deployed incorrectly
- stale gromox-mkprivate binary
