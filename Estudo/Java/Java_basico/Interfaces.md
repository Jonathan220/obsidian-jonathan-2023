
A partir do java 8, interfaces podem ter `default methods` ou `defender methods`. Isso possui implicações conceituais e práticas, que serão discutidas mais à frente.

![[Pasted image 20230926111932.png]]


## Inversão de controle, injeção de dependência

![[Pasted image 20230926124643.png]]


![[Pasted image 20230926124731.png]]

![[Pasted image 20230926125205.png]]

O BrazilTaxService é instanciado em outro lugar e é injetada no RentalService por meio do construtor.

Um exemplo do não uso de injeção de dependência seria instanciar o BrazilTaxService na classe RentalService.

![[Pasted image 20230926125529.png]]

O código acima gera um forte acoplamento, uma vez que caso seja necessário trocar a classe BrazilTaxService por outra classe, seria necessário alterar a classe RentalService.

![[Pasted image 20230926125919.png]]
