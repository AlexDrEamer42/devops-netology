# devops-netology
Глобальный .gitignore ничего не игнорирует.

/terraform/.gitignore игнорит:
- поддиректории .terraform/
- файлы c расширениями .tfstate и .tfstate.*
- логи с именем crash
- файлы с расширениями .tfvars и .tfvars.json
- файлы override.tf, override.tf.json, 
- файлы с именами, заканчивающиеся на _override.tf, _override.tf.json
- файлы .terraformrc и terraform.rc

123
