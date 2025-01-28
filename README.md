# Explicação da Função de Rejeição de Emails Spoofados 
Esta função é composta por várias partes que juntas ajudam a identificar e rejeitar emails que tentam falsificar o domínio local.

## Partes da Condição
1. `condition = ${if match_domain{$sender_address_domain}{+local_domains}}:`
+ Objetivo: Verificar se o domínio do endereço do remetente ($sender_address_domain) está na lista de domínios locais (+local_domains).
+ Resultado: Se o domínio do remetente for um dos seus domínios locais, esta condição será verdadeira.

2. `condition = ${if !match_ip{$sender_host_address}{+relay_hosts}}:`
+ Objetivo: Verificar se o endereço IP do servidor que está enviando o email ($sender_host_address) NÃO está na lista de hosts de retransmissão confiáveis (+relay_hosts).
+ Resultado: Se o IP do remetente não estiver na lista de hosts confiáveis, esta condição será verdadeira.

## Rejeitando Emails Spoofados
+ `deny:`
  + Objetivo: Esta ação nega a aceitação do email se ambas as condições acima forem verdadeiras.
  + Funcionamento: Se o domínio do remetente for um domínio local e o IP do remetente não for um dos IPs confiáveis, o email é rejeitado.

## Mensagens de Log e de Erro
+ `log_message = $sender_host_address attempted to spoof local domain $sender_address_domain:`
  + Objetivo: Registrar uma mensagem de log indicando que houve uma tentativa de spoofing.
  + Funcionamento: Adiciona uma entrada nos logs com o endereço IP do remetente e o domínio que ele tentou falsificar.

+ `message = Spoofed email from $sender_address_domain rejected:`
  + Objetivo: Enviar uma mensagem de erro de volta ao remetente.
  + Funcionamento: O remetente receberá uma mensagem indicando que o email foi rejeitado porque tentava falsificar um domínio local.

## Resumo do Funcionamento
Quando um email chega ao servidor EXIM:

1. Verificação do Domínio: O servidor verifica se o domínio do remetente é um dos seus domínios locais.
2. Verificação do IP: O servidor verifica se o IP do remetente é um IP confiável.
3. Rejeição do Email: Se o domínio é local mas o IP não é confiável, o email é rejeitado como tentativa de spoofing, e uma mensagem de log é gerada.

Dessa forma, essa função ajuda a prevenir que emails falsos, que tentam se passar por emails legítimos dos seus próprios domínios, sejam aceitos pelo seu servidor de emails.
