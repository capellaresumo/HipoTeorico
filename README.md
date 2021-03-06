# Projeto MAC0329

- Artur Alvarez		9292931
- Bruno Arico         8125459
- Gabriel Capella     8962078
- Nicolas Nogueira    9277541

### Estrutura do Projeto

A terceira parte do projeto consiste no acabamento do nosso computador HIPO.

Para realizar o acabamento foi necessário retrabalhar o FDX da parte 2 para acoplar uma ALU de 16 bits. Foi implementado uma forma de input/output com auxílio de uma pseudolinguagem interpretada por python que gera a entrada para o logisim.

### Acabamento do computador HIPO

Para o acabamento de nosso computador HIPO, três tarefas foram realizadas:

1. acoplamento da ALU de 16 bits na arquitetura de ciclo de instrução;
2. implementação de desvios condicionais;
3. funcionalidades de entrada/saída (I/O).

#### Acoplamento da ALU

A estrutura da ALU da primeira fase foi modificada de dois modos:

- As entradas passaram a ser de 16 bits e não mais 8 bits.
- O valor da saída da ALU somente é alterado se a mesma estiver habilitada pelo controlador. Isso permite, por exemplo, que após uma soma de dois números (operação que exige o acumulador em modo de leitura como uma das entradas da ALU), o valor resultante possa ser guardado no acumulador (operação que exige o acumulador em modo de escrita, ligado na saída da ALU).

Além da indicação de transbordamento (overflow), passamos também a indicar a ocorrência de divisão por zero. Na ocorrência de transbordamento e/ou divisão por zero, a execução do programa é interrompida. Para as modificações da ALU, foram utilizados os módulos aritméticos do Logisim.

O acoplamento da ALU com o ciclo de instrução nos permite a implementação das instruções correspondentes às cinco operações aritméticas com a instrução da pseudolinguagem utilizada:

 Decimal |  Hexadecimal | Instrução | Comando |              Descrição
---------|--------------|-----------|---------|---------------------------------------------
   21    |     0x15     | {ADD} XY  | AC + XY | Soma ACC com conteúdo do endereço XY
   22    |     0x16     | {SUB} XY  | AC - XY | Subtrai conteúdo do endereço XY de ACC
   23    |     0x17     | {MUL} XY  | AC * XY | Multiplica ACC pelo conteúdo do endereço XY
   24    |     0x18     | {DIV} XY  | AC / XY | Divide ACC pelo conteúdo do endereço XY

#### Desvios condicionais

As instruções de desvios condicionais foram implementadas no controlador junto com as instruções para o FDX e para a ALU. O controlador possui três flags que indicam quando o desvio condicional depende do valor do acumulador ser menor, igual, ou maior que zero. Essas flags são trabalhadas de forma que o desvio aconteça somente se necessário, ativando um bit de escrita no PC. A tabela abaixo indicam as 6 instruções utilizadas e seus respectivos desvios condicionais implementados, junto com as implementações da última etapa implementados com o FDX:

 Decimal |  Hexadecimal | Instrução |   Comando  |              Descrição
---------|--------------|-----------|------------|-----------------------------------------
   52    |     0x34     | {JLE} XY  |  if <= go  | Desvia para a instrução apontada por XY se ACC contém um valor menor ou igual a zero
   53    |     0x35     | {JDZ} XY  |  if != go  | Idem, diferente de zero
   54    |     0x36     | {JGT} XY  |  if >  go  | Idem, maior que zero
   55    |     0x37     | {JEQ} XY  |  if == go  | Idem, igual a zero
   56    |     0x38     | {JLT} XY  |  if <  go  | Idem, menor que zero
   57    |     0x39     | {JGE} XY  |  if >= go  | Idem, maior ou igual a zero
   11    |     0x0B     | {LDA} AB  | AC from AB | Carrega dado do endereço AB no acumulador
   12    |     0x0C     | {STA} AB  |  AC to AB  | Carrega acumulador no endereço AB
   50    |     0x32     | {NOP}     |            | Sem operação
   51    |     0x33     | {JMP} AB  |            | Salto não-condicional para o endereço AB
   70    |     0x46     | {STP}     |    end     | Fim da execução

#### Entrada/Saída

A entrada e saida foram feitos de forma que o usuário encaminha valores pelo terminal na faixa permitida pelo programa e a saída é exibida no terminal. Para testar o programa, criamos um mecanismo de pseudocódigo intuitivo que é interpretado por um programa em python, gerando um arquivo de saída para ser armazenado na RAM do Logisim.

A implementação das instruções de entrada e saída foi realizada do mesmo modo das demais instruções, sendo que os valores inseridos da entrada são recebidos por um componente teclado do Logisim e a saída é feita através de um componente terminal do Logisim.

 Decimal |  Hexadecimal | Instrução | Comando  |              Descrição
---------|--------------|-----------|----------|-------------------------------------------
   31    |     0x1F     | {INN} XY  |  read XY | Lê da entrada e armazena na posição XY
   41    |     0x29     | {PRN} XY  | print XY | Escreve na saída o conteúdo da posição XY

### Circuito

O circuito consiste na remodelagem dos anteriores, tanto a ALU quanto o FDX. O FDX foi remodelado para acoplar a ALU e receber operações de entrada e saída. A principal mudança foi a criação de um grid das instruções para o controlador determinar os pinos de saída nos diversos pontos do sistema e deixar o circuito mais entendível, mantendo o mesmo esquema do circuito sequencial da etapa anterior.

<!-- colocar img do circuito -->
<!-- colocar img do controlador -->

### Programa auxiliar

Para testar melhor o programa, criamos um mecanismo de interpretação de um pseudocódigo com uma estrutura muito comum a outras linguagens de programação, seria um modo intuitivo de criar o arquivo de entrada para a SRAM do Logisim. Na pasta programing estão três exemplos de programas feitos na pseudolinguagem criada: fibonacci.co (gera a sequência fibonacci), imprime100.co (imprime os 100 primeiros números naturais) e opera.co (recebe dois valores e imprime o resultado das operações da ALU entre o primeiro e o segundo).

Um programa em python recebe um desses arquivos criados na pseudolinguagem e gera um arquivo .cos que é o arquivo que será redirecionado para a RAM do logisim, na mesma pasta está um makefile que executa os exemplos.
