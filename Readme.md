# Cloud-init com Multipass

Este projeto reúne três configurações para preparar máquinas virtuais Ubuntu automaticamente com o Cloud-init.

| Etapa | Arquivo | O que será configurado |
| --- | --- | --- |
| 1 | `missao1.yaml` | Nginx com uma página HTML na porta 80 |
| 2 | `missao2-docker.yaml` | Docker com o container `nginxdemos/hello` na porta 8080 |
| 3 | `missao3-grafana.yaml` | Git, Docker, usuário `aluno` e Grafana na porta 3000 |

## Como executar

Com o Multipass instalado e funcionando, abra o terminal na pasta do projeto. Cada comando abaixo cria uma VM independente:

```powershell
multipass launch -n web --cloud-init missao1.yaml
multipass launch -n container --cloud-init missao2-docker.yaml
multipass launch -n monitoramento --cloud-init missao3-grafana.yaml
```

Você pode executar uma etapa por vez. Aguarde a configuração inicial terminar antes de acessar os serviços. Para acompanhar a etapa escolhida:

```powershell
multipass exec web -- sudo cloud-init status --wait
```

Troque `web` por `container` ou `monitoramento` para conferir as outras VMs.

## Como conferir o resultado

Consulte o endereço IPv4 de cada VM:

```powershell
multipass info web
multipass info container
multipass info monitoramento
```

No navegador, substitua `IP-DA-VM` pelo endereço da máquina correspondente:

- Nginx: `http://IP-DA-VM`
- Container de demonstração: `http://IP-DA-VM:8080`
- Grafana: `http://IP-DA-VM:3000` (primeiro acesso com `admin` / `admin`; altere a senha quando solicitado).

Para conferir o usuário e o arquivo da terceira etapa:

```powershell
multipass shell monitoramento
```

Dentro da VM, execute:

```bash
sudo -iu aluno
cat /home/aluno/bem-vindo.txt
```

## Validação dos arquivos

O workflow em `.github/workflows/validar-yaml.yml` verifica o cabeçalho `#cloud-config`, a sintaxe YAML e a estrutura aceita pelo Cloud-init em pushes, pull requests ou execução manual no GitHub Actions. A conferência dos serviços deve ser feita nas VMs, seguindo os passos acima.
