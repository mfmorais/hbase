# Bancos de dados NoSQL: Atividade Prática – HBase


### Agora, de dentro da imagem faça os sefuintes procedimentos:

1. Crie a tabela com 2 famílias de colunas:
a. personal-data
b. professional-data

```
    create 'italians', 'personal-data', 'professional-data'

    Created table italians
    Took 0.8732 seconds
    => Hbase::Table - italians
```

2. Importe o arquivo via linha de comando

```
    hbase shell ./italians.txt

    Took 0.8130 seconds
    Took 0.0045 seconds
    Took 0.0039 seconds
    Took 0.0077 seconds
    Took 0.0043 seconds
```

### Agora execute as seguintes operações:

1. Adicione mais 2 italianos mantendo adicionando informações como data
de nascimento nas informações pessoais e um atributo de anos de
experiência nas informações profissionais;

```
    put 'italians', '11', 'personal-data:name',  'Marcelo D2'
    put 'italians', '11', 'personal-data:city',  'Rio de Janeiro'
    put 'italians', '11', 'personal-data:birthdate',  '12/03/1969'
    put 'italians', '11', 'professional-data:role',  'Músico'
    put 'italians', '11', 'professional-data:salary',  '15000'
    put 'italians', '11', 'professional-data:experience',  '30' 

    put 'italians', '12', 'personal-data:name',  'Cristiano Ronaldo'
    put 'italians', '12', 'personal-data:city',  'Turim'
    put 'italians', '12', 'personal-data:birthdate',  '03/06/1985'
    put 'italians', '12', 'professional-data:role',  'Jogador de Futebol'
    put 'italians', '12', 'professional-data:salary',  '2000000'
    put 'italians', '12', 'professional-data:experience',  '12'
```

2. Adicione o controle de 5 versões na tabela de dados pessoais.

```
    alter 'italians', NAME => 'personal-data', VERSIONS => 5

    Updating all regions with the new schema...
    1/1 regions updated.
    Done.
    Took 2.2562 seconds
```

3. Faça 5 alterações em um dos italianos;

```
    put 'italians', '12', 'personal-data:city',  'Turim Update'
    put 'italians', '12', 'personal-data:birthdate',  '03/10/1985'
    put 'italians', '12', 'professional-data:role',  'Jogador de Futebol Update'
    put 'italians', '12', 'professional-data:salary',  '500000'
    put 'italians', '12', 'professional-data:experience',  '15'

    Took 0.0062 seconds
```

4. Com o operador get, verifique como o HBase armazenou o histórico.

```
    get 'italians', '11',{COLUMN=>'personal-data:birthdate',VERSIONS=>3}

    COLUMN                                                     CELL
    personal-data:birthdate                                    timestamp=1585864981137, value=03/10/1985
    personal-data:birthdate                                    timestamp=1585863698284, value=03/06/1985

    1 row(s)
    Took 0.0130 seconds
```

5. Utilize o scan para mostrar apenas o nome e profissão dos italianos.

```
    scan 'italians',{COLUMNS=>['personal-data:name','professional-data:role']}

    ROW                                                        COLUMN+CELL  
    1                                                          column=personal-data:name, timestamp=1585865902546, value=Paolo Sorrentino
    1                                                          column=professional-data:role, timestamp=1585865902600, value=Gestao Comercial
    10                                                         column=personal-data:name, timestamp=1585865903592, value=Giovanna Caputo
    10                                                         column=professional-data:role, timestamp=1585865903632, value=Comunicacao Institucional
    11                                                         column=personal-data:name, timestamp=1585863680902, value=Marcelo D2
    11                                                         column=professional-data:role, timestamp=1585863681120, value=M??sico
    12                                                         column=personal-data:name, timestamp=1585863698182, value=Cristiano Ronaldo
    12                                                         column=professional-data:role, timestamp=1585864981178, value=Jogador de Futebol Update
    2                                                          column=personal-data:name, timestamp=1585865902647, value=Domenico Barbieri
    2                                                          column=professional-data:role, timestamp=1585865902693, value=Psicopedagogia  
    3                                                          column=personal-data:name, timestamp=1585865902725, value=Maria Parisi
    3                                                          column=professional-data:role, timestamp=1585865902776, value=Optometria  
    4                                                          column=personal-data:name, timestamp=1585865902846, value=Silvia Gallo
    4                                                          column=professional-data:role, timestamp=1585865902893, value=Engenharia Industrial Madeireira
    5                                                          column=personal-data:name, timestamp=1585865902955, value=Rosa Donati
    5                                                          column=professional-data:role, timestamp=1585865903053, value=Mecatronica Industrial
    6                                                          column=personal-data:name, timestamp=1585865903102, value=Simone Lombardo
    6                                                          column=professional-data:role, timestamp=1585865903188, value=Biotecnologia e Bioquimica
    7                                                          column=personal-data:name, timestamp=1585865903221, value=Barbara Ferretti
    7                                                          column=professional-data:role, timestamp=1585865903263, value=Libras
    8                                                          column=personal-data:name, timestamp=1585865903296, value=Simone Ferrara  
    8                                                          column=professional-data:role, timestamp=1585865903388, value=Engenharia de Minas
    9                                                          column=personal-data:name, timestamp=1585865903463, value=Vincenzo Giordano
    9                                                          column=professional-data:role, timestamp=1585865903524, value=Marketing
    12 row(s)
    Took 0.1091 seconds
```

6. Apague os italianos com row id ímpar

```
    deleteall 'italians', ['1', '3', '5','7','9','11']

    Took 0.0144 seconds
```

7. Crie um contador de idade 55 para o italiano de row id 5

```
    incr 'italians','5','personal-data:idade'

    COUNTER VALUE = 1
    Took 0.0398 seconds
```

8. Incremente a idade do italiano em 1

```
    incr 'italians','5','personal-data:idade'

    COUNTER VALUE = 1
    Took 0.0398 seconds
```