# Infraestrutura de Deploy e Publicação

## 1. Visão Geral

Esta documentação apresenta a infraestrutura prevista para a publicação do DoaFácil. Os usuários acessarão a aplicação pelo navegador ou pela PWA, por meio de HTTPS. A solução será hospedada em uma máquina virtual Linux em nuvem, com contêineres Docker para Nginx, Laravel/PHP-FPM e MySQL. Um volume persistente armazenará os dados do banco.

O fluxo previsto é: Browser/PWA → Nginx → Laravel/PHP-FPM → MySQL. O código ficará no GitHub, e o GitHub Actions poderá executar validações, gerar a imagem Docker e apoiar a publicação. Esta é uma proposta de arquitetura; nenhum ambiente de produção foi implantado. Os [diagramas de implantação](../diagramas/implantacao.png) e [DevOps](../diagramas/devops.png) complementam esta descrição.

## 2. Infraestrutura Escolhida

A opção escolhida é uma **Máquina Virtual Linux em provedor de nuvem**. O provedor ainda não foi definido. A máquina virtual hospedará o Docker e permitirá administrar o sistema operacional e os serviços necessários à aplicação.

Essa organização pode ser reproduzida em diferentes provedores com ajustes de configuração de rede e armazenamento, sem alterar os componentes principais da arquitetura.

## 3. Docker

O Docker será usado para padronizar o ambiente e executar Nginx, Laravel/PHP-FPM e MySQL em contêineres separados. Isso isola os serviços, facilita a implantação e permite atualizá-los por meio de novas imagens. Também favorece a portabilidade da solução entre servidores Linux compatíveis. Essas características são descritas na [documentação do Docker](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/).

## 4. Servidor Web

O Nginx receberá as requisições dos usuários, atuará como servidor web e proxy reverso e encaminhará as requisições da aplicação ao Laravel/PHP-FPM. A configuração de HTTPS será necessária na publicação para proteger a comunicação externa. O encaminhamento ao PHP-FPM poderá utilizar FastCGI, conforme a [documentação do Nginx](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy).

## 5. Aplicação

O Laravel será responsável pelos casos de uso e pelas regras de negócio do DoaFácil. O PHP-FPM executará o código PHP da aplicação no contêiner correspondente, recebendo as requisições encaminhadas pelo Nginx.

## 6. Banco de Dados

O MySQL armazenará dados de usuários, instituições, campanhas e doações. Seu contêiner utilizará um volume persistente para que os dados permaneçam disponíveis quando o contêiner for recriado, desde que o volume seja preservado. O volume não substitui cópias de segurança. A persistência independente do ciclo de vida do contêiner é explicada na [documentação de volumes do Docker](https://docs.docker.com/engine/storage/volumes/).

## 7. Por que utilizar Cloud

A hospedagem em nuvem facilita a disponibilização pública da aplicação e dispensa a compra e a manutenção de um servidor físico pela equipe. Também permite ajustar os recursos da máquina virtual conforme a necessidade e oferece uma base para expansão futura. A publicação pode ser repetida em outra máquina virtual, mantendo a organização dos contêineres.

A disponibilidade do sistema ainda dependerá da configuração, da operação do provedor e da manutenção da aplicação; a escolha por nuvem, por si só, não garante funcionamento ininterrupto.

## 8. Comparação com Self-Hosted

| Aspecto | Máquina virtual em nuvem | Servidor local (*self-hosted*) |
| --- | --- | --- |
| Investimento inicial | Não exige compra de servidor físico pela equipe. | Exige aquisição ou disponibilidade de equipamento próprio. |
| Acesso público | Pode ser configurado na rede do provedor. | Depende da conexão e da configuração da rede local. |
| Ajuste de recursos | Pode ser feito pela troca da configuração da máquina virtual, conforme as opções do provedor. | Pode exigir ampliação ou troca de hardware. |
| Responsabilidade operacional | O provedor mantém o hardware; a equipe continua responsável pelo sistema operacional e pela aplicação. | A equipe também responde pelo hardware, pela energia e pela rede local. |
| Controle físico | Menor controle direto sobre o equipamento. | Maior controle físico, útil em alguns ambientes internos. |

As duas opções são viáveis. Para o escopo inicial do DoaFácil, a máquina virtual em nuvem reduz a necessidade de infraestrutura física própria sem exigir uma arquitetura complexa.

## 9. Comparação entre provedores de nuvem

Os provedores abaixo oferecem serviços de máquinas virtuais capazes de hospedar a solução proposta:

| Provedor | Serviço de máquina virtual |
| --- | --- |
| AWS | [Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Instances.html) |
| Microsoft Azure | [Azure Virtual Machines](https://learn.microsoft.com/azure/virtual-machines/overview) |
| Google Cloud | [Compute Engine](https://docs.cloud.google.com/compute/docs/instances) |
| Oracle Cloud | [OCI Compute](https://docs.oracle.com/en-us/iaas/Content/Compute/Concepts/computeoverview.htm) |

Não é necessário escolher um provedor nesta etapa. O uso de Linux e Docker reduz a dependência de recursos exclusivos de uma plataforma. Uma escolha futura poderá considerar custos, limites de recursos e condições de operação, sem mudar a arquitetura básica.

## 10. Justificativa Final

Uma máquina virtual Linux em nuvem, combinada com Docker, oferece simplicidade de implantação, separação dos serviços e portabilidade compatíveis com o porte acadêmico do DoaFácil. A proposta atende à publicação inicial e permite ajustar recursos posteriormente, sem tornar a infraestrutura mais complexa do que o projeto exige.
