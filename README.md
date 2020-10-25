# Changing-Active-Directory-user-password-in-Python-3.x
<br>
First, install latest ldap3:<br>
sudo pip3 install ldap


<br>
<br>import ldap3
<br>
<br>SERVER='127.0.0.1'
<br>BASEDN="DC=domain,DC=com"
<br>USER="user_domain_login_name@domain.com"
<br>CURREENTPWD="current_password"
<br>NEWPWD="new_password"
<br>
<br>SEARCHFILTER='(&(userPrincipalName='+USER+')(objectClass=person))'
<br>
<br>USER_DN=""
<br>USER_CN=""
<br>
<br>ldap_server = ldap3.Server(SERVER, get_info=ldap3.ALL)
<br>conn = ldap3.Connection(ldap_server, USER, CURREENTPWD, auto_bind=True)
<br>conn.start_tls()
<br>print(conn)
<br>
<br>conn.search(search_base = BASEDN,
<br>         search_filter = SEARCHFILTER,
<br>         search_scope = ldap3.SUBTREE,
<br>         attributes = ['cn', 'givenName', 'userPrincipalName'],
<br>         paged_size = 5)
<br>
<br>for entry in conn.response:
<br>    if entry.get("dn") and entry.get("attributes"):
<br>        if entry.get("attributes").get("userPrincipalName"):
<br>            if entry.get("attributes").get("userPrincipalName") == USER:
<br>                USER_DN=entry.get("dn")
<br>                USER_CN=entry.get("attributes").get("cn")
<br>
<br>print("Found user:", USER_CN)
<br>
<br>if USER_DN:
<br>    print(USER_DN)
<br>    print(ldap3.extend.microsoft.modifyPassword.ad_modify_password(conn, USER_DN, NEWPWD, CURREENTPWD,  controls=None))
<br>else:
<br>    print("User DN is missing!")
<br>
