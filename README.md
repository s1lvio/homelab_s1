🚀 Projeto Home Lab: Servidor de Armazenamento Nuvem Local e RemotaEste projeto foi desenvolvido com foco didático, servindo como material prático de consulta e aprendizagem para a montagem de um servidor de arquivos caseiro (Home Lab). A construção deste laboratório proporciona uma excelente introdução prática a conceitos de administração Linux, redes, segurança, gerenciamento de banco de dados, containers e virtualização.O objetivo principal é estabelecer uma nuvem privada segura para armazenamento e backup de dados pessoais, utilizando hardware reaproveitado.🗺️ Arquitetura e Diagrama da RedeAbaixo está o mapeamento físico e lógico de como os dispositivos se comunicam dentro da rede doméstica:               [ Provedor de Internet ]
                          │
                    ┌─────▼─────┐
                    │  ONT/ONU  │
                    └─────┬─────┘
                          │ (Cabo)
                    ┌─────▼─────┐
                    │ Roteador  │◄──────── (Wi-Fi) ────────► [ Dispositivos Móveis ]
                    └─┬───────┬─┘                            (Smartphones, IoT, etc.)
                      │       │
             (Cabo)   │       │   (Cabo)
         ┌────────────┘       └────────────┐
   ┌─────▼─────┐                     ┌─────▼─────┐
   │    PC     │                     │ Servidor  │
   │ Principal │                     │ Home Lab  │
   └───────────┘                     └───────────┘
🛠️ Especificações de Hardware e SoftwarePara demonstrar que é perfeitamente possível iniciar com baixo custo, o servidor foi construído a partir de um notebook antigo adaptado:Dispositivo: Notebook HP 14 (Hewlett-Packard)Processador: Intel Celeron N2830 @ 2.16GHzMemória RAM: 4 GiB DDR3 @ 1333MHzArmazenamento: SSD Western Digital (WD) de 120GBInterface de Rede: RJ45 Fast Ethernet 10/100 MbpsSistema Operacional: Ubuntu Server 25.10 (Kernel Linux 6.17.0-35-generic)Serviço de Nuvem: Nextcloud Hub (via Snap)🏁 Passo a Passo da InstalaçãoFase 1: Preparação do Sistema OperacionalDownload da ISO: Baixe a imagem estável diretamente no Site Oficial do Ubuntu Server.Criação da Mídia de Boot: Utilize a ferramenta Ventoy para gravar a ISO em seu pendrive de forma simples. Em caso de dúvidas, consulte a Documentação de Início do Ventoy.Instalação do OS: Conecte o pendrive ao notebook e siga as instruções padrão detalhadas na Guia Oficial de Instalação Básica do Ubuntu.Atualização Inicial: Com o sistema instalado e logado pela primeira vez, atualize a lista de pacotes e faça o upgrade geral executando:sudo apt update && sudo apt upgrade -y
Configuração de IP Fixo (Netplan)Para evitar que o roteador altere o IP do seu servidor após reinicializações, edite as configurações do Netplan.Acesse o diretório /etc/netplan e abra o arquivo de configuração .yaml existente utilizando o editor nano:sudo nano /etc/netplan/[nomedoarquivo].yaml
(Substitua [nomedoarquivo] pelo nome exato do arquivo encontrado na sua pasta)Ajuste o conteúdo do arquivo para o modelo abaixo, configurando a sua interface de rede e a faixa de IP de sua preferência:network:
  version: 2
  renderer: networkd
  ethernets:
    [nome-da-interface]:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
Salve o arquivo (Ctrl + O, Enter e depois saia com Ctrl + X) e aplique as configurações com o comando:sudo netplan apply
Fase 2: Instalação e Configuração do NextcloudA instalação através de pacotes Snap traz isolamento e facilidade na hora de gerenciar e atualizar os serviços.1. Instalação BásicaPara baixar e iniciar a pilha do Nextcloud automaticamente, rode:sudo snap install nextcloud
2. Criação do Usuário AdministradorCrie as credenciais para o seu login inicial na interface web. Substitua admin e test1234 pelo usuário e pela senha forte que deseja usar:sudo nextcloud.manual-install admin test1234
3. Configuração de Domínios Confiáveis (Trusted Domains)Por padrão, o Nextcloud só permite conexões originadas do endereço localhost. Para permitir que computadores na sua rede local acessem a interface:Veja quais domínios estão atualmente ativos na configuração:sudo nextcloud.occ config:system:get trusted_domains localhost
Adicione o IP estático do seu servidor à lista de confiança (substitua o IP pelo endereço correto que você configurou no seu servidor):sudo nextcloud.occ config:system:set trusted_domains 1 --value=192.168.0.152
4. Proteção por HTTPS (Criptografia SSL)Ative a segurança de transporte para garantir que seus dados trafeguem de forma criptografada pela rede doméstica:sudo nextcloud.enable-https self-signed
🛡️ Configuração de Segurança (Firewall UFW)Por segurança, garanta que apenas as portas essenciais do servidor fiquem abertas a conexões de outros computadores da rede:Permita a conexão remota segura via SSH:sudo ufw allow ssh
Permita a entrada de tráfego seguro na porta padrão do Nextcloud (HTTPS):sudo ufw allow https
Ative o Firewall para aplicar as regras definidas:sudo ufw enable
🌐 Próximos Passos: Interface GráficaParabéns! Toda a camada crítica e os serviços base estão operacionais via terminal. Agora o restante das etapas pode ser feito visualmente de forma confortável.Abra o seu navegador web favorito em qualquer outro computador ou celular conectado na sua rede local e digite o IP do servidor:[https://192.168.0.152](https://192.168.0.152)
⚠️ Nota: Como utilizamos um certificado SSL autoassinado (self-signed), seu navegador exibirá um alerta de segurança na primeira visita informando que o site não é reconhecido por uma autoridade certificadora global. Isso é normal e esperado para ambientes locais. Basta clicar em Avançado e selecionar Continuar para o site (não seguro) para acessar seu Nextcloud.
