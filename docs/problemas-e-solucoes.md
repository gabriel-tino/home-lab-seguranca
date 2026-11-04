# Problemas e soluções

Registro dos problemas que surgiram durante a montagem do lab, com o que causou cada um e como resolvi.

---

## 1. Baixei a ISO errada (Ubuntu Desktop em vez de Server)

- **Sintoma:** ao ligar a VM apareceu uma área de trabalho gráfica, quando eu esperava um terminal em modo texto.
- **Causa:** eu havia baixado a ISO do Ubuntu **Desktop**, e não a do Ubuntu **Server**. A do Server traz `live-server` no nome do arquivo.
- **Solução:** baixei a ISO do Ubuntu Server no site oficial, criei uma VM nova e instalei a versão só de terminal.
- **Aprendizado:** conferir o nome do arquivo antes de instalar. Para o lab, o Server é melhor: consome menos memória e disco e tem menos superfície de ataque, por não ter interface gráfica.

---

## 2. Não anotei o usuário da VM

- **Sintoma:** depois da instalação do Ubuntu Server, eu não lembrava o nome de usuário para fazer login.
- **Causa:** o Ubuntu não tem usuário padrão. O usuário é o que se cria na instalação, e eu não o anotei.
- **Solução:** o prompt do terminal mostra o usuário e a máquina no formato `usuario@hostname` (no meu caso, `gabriel@ubuntuVM`). 
- **Aprendizado:** manter um arquivo de anotações do lab com usuários, hostnames e IPs de cada VM (usando senhas só de laboratório).

---

## 3. Dificuldade para digitar o arquivo YAML no terminal

- **Sintoma:** não consegui escrever o arquivo de configuração de rede (netplan) no terminal da VM. O console do Ubuntu Server não aceita colar texto, e o formato exige indentação exata.
- **Causa:** o arquivo é YAML, em que a indentação é feita com **espaços** (nunca Tab) e cada nível tem 2 espaços a mais que o anterior. O nano não repete a indentação automaticamente, então preciso digitar cada espaço. O console também não permite colar.
- **Solução:** digitei o arquivo linha por linha no `nano`, conferindo os espaços de cada nível. Como o editor abriu como "New Buffer" (sem nome), usei `Ctrl+O` para salvar em `/etc/netplan/99-lab.yaml`. Em seguida ajustei a permissão com `sudo chmod 600` e apliquei com `sudo netplan apply`. O `ip a` confirmou o IP `10.10.10.20/24` na interface `enp0s3`.
- **Aprendizado:** como funciona a estrutura do netplan (`dhcp4`, `addresses`, máscara `/24`) e por que um arquivo novo com número maior (`99-`) sobrepõe a configuração original. O arquivo final está em [`configs/99-lab.yaml`](../configs/99-lab.yaml).

---

## 4. A opção "Rede interna" não aparecia no VirtualBox

- **Sintoma:** na configuração de rede da VM, só havia NAT e Bridge na lista.
- **Causa:** nas configurações mudei a opção de "Basic" para "Expert"
- **Solução:** encontrei a opção e configurei os dois adaptadores em **Rede interna**, com o mesmo nome (`labnet`).
- **Aprendizado:** NAT e Bridge usam a conexão do notebook e, portanto, a rede do trabalho. Só a **Rede interna** mantém o lab isolado, e por isso foi a escolha para este projeto.

---

## 5. Dúvida: o Wi-Fi do notebook interfere no isolamento?

- **Sintoma:** como o notebook usa Wi-Fi, fiquei na dúvida se isso afetava as VMs.
- **Causa:** a dúvida vinha de misturar a rede física do notebook com as redes virtuais do VirtualBox.
- **Solução:** entendi que a Rede interna é totalmente virtual: o tráfego entre as VMs fica dentro do VirtualBox e não passa pela placa Wi-Fi. A prova é que o `ping 8.8.8.8` falha nas duas VMs.
- **Aprendizado:** o tipo de conexão do hospedeiro (Wi-Fi ou cabo) só importa nos modos NAT e Bridge.
