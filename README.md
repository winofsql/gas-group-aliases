# gas-group-aliases

```javascript
function listGroupAliasesToSheet() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  sheet.clearContents(); // 既存の内容をクリア
  sheet.appendRow(['名称','Group Email', '別名']); // ヘッダーを設定

  // 特定のドメインを指定
  var domain = ''; // ここに特定のドメインを設定
  var pageToken;
  do {
    var response = AdminDirectory.Groups.list({
      'domain': domain,
      'maxResults': 200,
      'pageToken': pageToken
    });

    var groups = response.groups;
    if (groups && groups.length > 0) {
      for (var i = 0; i < groups.length; i++) {
        var group = groups[i];
        var firstAlias = true; // 最初の別名かどうかをチェック

        // グループの別名を取得
        var aliases = group.aliases;
        if (aliases && aliases.length > 0) {
          for (var j = 0; j < aliases.length; j++) {
            // 最初の別名のみグループのメールアドレスを表示
            if (firstAlias) {
              sheet.appendRow([group.name, group.email, aliases[j]]);
              firstAlias = false;
            } else {
              sheet.appendRow(['', '', aliases[j]]);
            }
          }
        } else {
          // 別名がない場合は、グループのみを出力
          sheet.appendRow([group.name, group.email, '']);
        }
      }
    }

    // 次のページのための pageToken を取得
    pageToken = response.nextPageToken;
  } while (pageToken);
}
```
