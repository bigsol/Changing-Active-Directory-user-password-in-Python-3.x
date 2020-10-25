# Changing-Active-Directory-user-password-in-Python-3.x

First, install latest ldap3:
sudo pip3 install ldap


import ldap3

SERVER='127.0.0.1'
BASEDN="DC=domain,DC=com"
USER="user_domain_login_name@domain.com"
CURREENTPWD="current_password"
NEWPWD="new_password"

SEARCHFILTER='(&(userPrincipalName='+USER+')(objectClass=person))'

USER_DN=""
USER_CN=""

ldap_server = ldap3.Server(SERVER, get_info=ldap3.ALL)
conn = ldap3.Connection(ldap_server, USER, CURREENTPWD, auto_bind=True)
conn.start_tls()
print(conn)

conn.search(search_base = BASEDN,
         search_filter = SEARCHFILTER,
         search_scope = ldap3.SUBTREE,
         attributes = ['cn', 'givenName', 'userPrincipalName'],
         paged_size = 5)

for entry in conn.response:
    if entry.get("dn") and entry.get("attributes"):
        if entry.get("attributes").get("userPrincipalName"):
            if entry.get("attributes").get("userPrincipalName") == USER:
                USER_DN=entry.get("dn")
                USER_CN=entry.get("attributes").get("cn")

print("Found user:", USER_CN)

if USER_DN:
    print(USER_DN)
    print(ldap3.extend.microsoft.modifyPassword.ad_modify_password(conn, USER_DN, NEWPWD, CURREENTPWD,  controls=None))
else:
    print("User DN is missing!")
