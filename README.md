# Simple HC Vault UI

**Docker:**

$ mkdir -p ./vault/{config,data,file,policies}

Скопируйте vault.json в ./vault/config/vault.json

$ docker-compose up -d

http://localhost:8200


**Gitlab:**

$ vault login

$ vault kv put secret/projects/demo-vault/mariadb login=testuser password=testpassword

$ vault policy write project-test - <<EOF
path "secret/projects/demo-vault/*" {
  capabilities = [ "read" ]
}
EOF

$ vault write auth/jwt/role/project-test - <<EOF
{
  "role_type": "jwt",
  "policies": ["project-test"],
  "token_explicit_max_ttl": 60,
  "user_claim": "user_email",
  "bound_claims": {
    "project_id": "294",
    "ref": "main",
    "ref_type": "branch"
  }
}
EOF


**Asible:**

$ vault login

$ vault kv put -tls-skip-verify ansible/serverlab/production/db dbusername=wp_user dbpassword=passwd123  dbhost=vault.exampe.local dbname=blog

$ vault policy write ansible - <<EOF
path "ansible/*" {
  capabilities = [ "read", "list" ]
}
EOF

$ sudo pip3 install hvac

$ ansible-playbook -i hosts -l servers playbook.yml --tags "test" # Покажет результат в консоли

$ ansible-playbook -i hosts -l servers playbook.yml --tags "update_wp_config" # Создаст файл /tmp/wp-config.php
