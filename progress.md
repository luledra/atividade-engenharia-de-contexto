## Objetivo

- Fazer o teste `test_fixed_coupon_never_negative` passar sem quebrar os demais testes em `tests/test_pricing.py`.
- Não mudar a assinatura pública da função `apply_discount(order, coupon)`, pois o checkout depende dela.

## Abordagem

- Manter o código compatível com Python 3.11.
- Aplicar `max(Decimal("0"), ...)` na função `_fixed_off` recebendo o total parcial.
- Escrever `test_stacked_coupons` cobrindo dois cupons percentuais em sequência.
- Rodar o comando `pytest tests/test_pricing.py -q` e verificar se todos os testes passaram.
- Verificar se a a assinatura pública da função `apply_discount(order, coupon)` não foi alterada.

## Passos concluídos

- Identificou que o teste `test_discount_applied_after_coupon` espera 85.50 e estava recebendo 85.0.
- Identificou que a causa do erro estava na função `_percent_off(order, c)`.
- Modificou a função `_percent_off(order, c)` para receber o total parcial a invés de usar `order.subtotal`.
- Identificou que o teste `test_discount_applied_after_coupon` agora estava recebendo 85.49999999999999.
- Modificou a função `_percent_off(order, c)` para usar `Decimal` com arredondamento `ROUND_HALF_UP` e `quantize(Decimal("0.01"))`, que é o padrão do projeto para representar dinheiro.
- Identificou que o teste `test_discount_applied_after_coupon` passou, porém o teste `test_fixed_coupon_never_negative` quebrou.

## Falha atual

- O teste `test_fixed_coupon_never_negative` falha para um cupom fixo de 5 sobre um pedido de 3,00, que deixa o total em -2,00.
- A causa da falha está na função `_fixed_off`, que não limita o desconto ao total disponível.