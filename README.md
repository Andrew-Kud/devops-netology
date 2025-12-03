# devops-netology

Какие файлы будут проигнорированны:

1.  .terraform/   - локальная директория
2.  *.tfstate     - основной файл состояния
3.  *.tfstate.*   - резервные копии и другие версии state файлов
4.  crash.log     - основной лог крашей
5.  crash.*.log   - все логи крашей с расширениями
6.  *.tfvars      - файлы переменных
7.  *.tfvars.json - json-файлы переменных
8.  override.tf
9.  override.tf.json
10. *_override.tf - любые файлы с суффиксом _override.tf
11. *_override.tf.json
12. .terraform.tfstate.lock.info - временный файл блокировки при выполнении terraform apply
13. .terraformrc - конфигурационный файл
14. terraform.rc - альтернативный конфиг-файл

