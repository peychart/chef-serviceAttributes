# chef-serviceAttributes-cookbook

 This chef cookbook allows to simulate service environments in service definitions by the use of data bags...

## Supported Platforms

 ubuntu/debian

## Attributes

<table>
  <tr>
    <th>Key</th>
    <th>Type</th>
    <th>Description</th>
    <th>Default</th>
  </tr>
  <tr>
    <td><tt>['chef-serviceAttributes']['precedence']</tt></td>
    <td>String</td>
    <td>Precedence to apply in the next run</td>
    <td><tt>normal (see: https://docs.getchef.com/essentials_cookbook_attribute_files.html#attribute-types)</tt></td>
  </tr>
  <tr>
    <td><tt>['chef-serviceAttributes']['secret_key']</tt></td>
    <td>String/boolean</td>
    <td>location of the encryption key (TRUE for default)</td>
    <td><tt>FALSE</tt></td>
  </tr>
  <tr>
    <td><tt>['chef-serviceAttributes'][*]</tt></td>
    <td>String/StringArea</td>
    <td>Data bag where to found the fqdn item</td>
    <td><tt>nil</tt></td>
  </tr>
</table>

## Usage

 Default attributes of any cookbook called after this one can be completed within data bags whose items are the service id...

 In these data bags, when an attribute name is preceded with '!', all initial values of arrays or hashs are deleted before update.

 (1): Dots are not allowed in databags items (only alphanumeric); must be substitute by underscores...

eg. in data bag "service":
<pre>
{
  "id": "owncloud",
  "chef-lvm": {
    "lvm_volume_group": [
      {
        "name": "ubuntu-1404-vg",
        "physical_volumes": [
          "/dev/sdb"
        ]
      },
      {
        "name": "vg_data",
        "physical_volumes": [
          "/dev/sdc"
        ],
        "logical_volume": [
          {
            "name": "owncloud",
            "size": "100%FREE",
            "filesystem": "reiserfs",
            "mount_point": {
              "location": "/var/www/owncloud",
              "options": "noatime,notail,nobootwait"
            }
          }
        ]
      }
    ]
  }
  "chef-owncloud": {
    "!otheroptions": [
      "'blacklisted_files' => array('.htaccess')",
      "'overwritewebroot' => '/owncloud'",
      "'proxy' => 'login:password@squid.a1a2.srv.gov.pf:3128'",
      "'default_language' => 'fr'",
      "'enable_avatars' => true",
      "",
      "'default_language' => 'fr'",
      "'enable_avatars' => true",
      "",
      "'appstoreenabled' => false",
      "'loglevel' => '0'"
    ]
  },
  "chef-iptables": {
    "ipv4rules": {
      "filter": {
        "INPUT": {
          "https": [
            "--protocol tcp --dport 80 --match state --state NEW --jump ACCEPT",
            "--protocol tcp --dport 443 --match state --state NEW --jump ACCEPT"
          ]
        },
        "OUTPUT": {
          "default": "-j ACCEPT"
        }
      }
    }
  }
}
</pre>


### chef-serviceAttributes::default

Include `chef-serviceAttributes` in your node's `run_list`:

```json
{
  "override_attributes" => {
    "chef-serviceAttributes" => {
      "service" => "onwcloud"    // Can be a stringsArray...
    }
  },
  "run_list" => [
    "recipe[chef-serviceAttributes::default]",
    "recipe[chef-owncloud::default]"
  ]
}
```

WARNING: don't use the same attribut name between succesive roles to define the databag name(s)

 So, node.default is then settled from the data bag definitions, on the item "name of the role"; then node.'precedence' = node.default. An other cookbook::recipes can be applied...

## License and Authors

Author:: PE, pf. (<peychart@mail.pf>)
