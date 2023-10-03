<img src="https://raw.githubusercontent.com/rhatiro/Curso_EBAC-Profissao_Cientista_de_Dados/main/ebac-course-utils/media/logo/newebac_logo_black_half.png" alt="ebac-logo">

---

# **Profissão: Cientista de Dados**
### **Módulo 5** | Limpeza e preparação de dados | Exercício 1

Aluno [Paulo Roberto](https://www.linkedin.com/in/paulo-roberto/)<br>
Data: 30 de stembro de 2023.

---

# Módulo 5 Tarefa 1
## Base de nascidos vivos do DataSUS
O DataSUS disponibiliza diversos arquivos de dados com relação a seus segurados, conforme a [lei da transparência de informações públicas](https://www.sisgov.com/transparencia-acesso-informacao/#:~:text=A%20Lei%20da%20Transpar%C3%AAncia%20(LC,em%20um%20site%20na%20internet.).

Essas informações podem ser obtidas pela internet [aqui](http://www2.datasus.gov.br/DATASUS/index.php?area=0901&item=1). Como o processo de obtenção desses arquivos foge um pouco do nosso escopo, deixamos o arquivo ```SINASC_RO_2019.csv``` já como vai ser encontrado no DataSUS. O dicionário de dados está no arquivo ```estrutura_sinasc_para_CD.pdf``` (o nome do arquivo tal qual no portal do DataSUS).

### Nosso objetivo
Queremos deixar uma base organizada para podermos estudar a relação entre partos com risco para o bebê e algumas condições como tempo de parto, consultas de pré-natal etc.

#### Preparação da base
1. Carregue a base 'SINASC_RO_2019.csv'. Conte o número de registros e o número de registros não duplicados da base. Dica: você aprendeu um método que remove duplicados, encadeie este método com um outro método que conta o número de linhas. **Há linhas duplicadas?**  

2. Conte o número de valores *missing* por variável.  

3. Ok, no item anterior você deve ter achado pouco prático ler a informação de tantas variáveis, muitas delas nem devem ser interesantes. Então crie uma seleção dessa base somente com as colunas que interessam. São elas:
```
['LOCNASC', 'IDADEMAE', 'ESTCIVMAE', 'ESCMAE', 'QTDFILVIVO',
    'GESTACAO', 'GRAVIDEZ', 'CONSULTAS', 'APGAR5']
```
Refaça a contagem de valores *missings*.  

4. Apgar é uma *nota* que o pediatra dá ao bebê quando nasce de acordo com algumas características associadas principalmente à respiração. Apgar 1 e Apgar 5 são as notas 1 e 5 minutos do nascimento. Apgar5 será a nossa variável de interesse principal. Então remova todos os registros com Apgar5 não preenchido. Para esta seleção, conte novamente o número de linhas e o número de *missings*.  

5. observe que as variáveis ```['ESTCIVMAE', 'CONSULTAS']``` possuem o código ```9```, que significa *ignorado*. Vamos assumir que o não preenchido é o mesmo que o código ```9```.<br>
6. Substitua os valores faltantes da quantitativa (```QTDFILVIVO```) por zero.  
7. Das restantes, decida que valore te parece mais adequado (um 'não preenchido' ou um valor 'mais provável' como no item anterior) e preencha. Justifique. Lembre-se de que tratamento de dados é trabalho do cientista, e que estamos tomando decisões a todo o momento - não há necessariamente certo e errado aqui.  
8. O Apgar possui uma classificação indicando se o bebê passou por asfixia:
- Entre 8 e 10 está em uma faixa 'normal'.
- Entre 6 e 7, significa que o recém-nascido passou por 'asfixia leve'.
- Entre 4 e 5 significa 'asfixia moderada'.
- Entre 0 e 3 significa 'asfixia severa'.  

Crie uma categorização dessa variável com essa codificação e calcule as frequências dessa categorização.  
<br>
9. Renomeie as variáveis para que fiquem no *snake case*, ou seja, em letras minúsculas, com um *underscore* entre as palávras. Dica: repare que se você não quiser criar um *dataframe* novo, você vai precisar usar a opção ```inplace = True```.


```python
import pandas as pd
import requests
```

- Este código é importante para uma verificação inicial da qualidade de um arquivo CSV. Ele oferece dados sobre o tamanho do conjunto de dados e a detecção de duplicatas, informações cruciais para análises futuras e, se preciso, para aprimoramento da qualidade dos dados.


```python
# 1) Carregue a base 'SINASC_RO_2019.csv'. Conte o número de registros e o número
# de registros não duplicados da base. Dica: você aprendeu um método que remove
# duplicados, encadeie este método com um outro método que conta o número de linhas.
# Há linhas duplicadas?

sinasc = pd.read_csv('SINASC_RO_2019.csv')
print('Número de registro: ')
print(sinasc.shape[0])

print('\nRegistros não duplicados: ')
print(len(sinasc.drop_duplicates()))

if len(sinasc) - len(sinasc[~sinasc.duplicated()]) == 0:
  print('\n**Nenhuma linha duplicada.**')
else:
  print('**Há linhas duplicadas.**')
```

    Número de registro: 
    27028
    
    Registros não duplicados: 
    27028
    
    **Nenhuma linha duplicada.**
    


- Essa análise para detectar valores ausentes em cada variável do DataFrame. Isso é essencial para avaliar a qualidade dos dados, pois valores ausentes podem impactar análises e modelos. A saída do código mostra o número de valores ausentes em cada variável, orientando a tomada de decisões sobre como lidar com eles. Esta etapa é fundamental na preparação de dados antes de análises estatísticas ou modelagem.


```python
# 2) Conte o número de valores missing por variável.

print('Valores de missing por variável:')
print(sinasc.isna().sum())

print('\nNúmero total de valores missing:')
print(sinasc.isna().sum().sum())
```

    Valores de missing por variável:
    ORIGEM          0
    CODESTAB      115
    CODMUNNASC      0
    LOCNASC         0
    IDADEMAE        0
                 ... 
    munResUf        0
    munResLat       1
    munResLon       1
    munResAlt       1
    munResArea      1
    Length: 69, dtype: int64
    
    Número total de valores missing:
    121594
    

- Neste código, foi realizada uma análise eficiente e direcionada dos dados da base 'sinasc'. As colunas relevantes foram selecionadas, seguidas pela contagem de valores ausentes nessas colunas escolhidas. Esse processo simplificado proporciona uma compreensão rápida da qualidade dos dados nas variáveis de interesse, sendo uma etapa crucial na preparação dos dados para análises subsequentes. O tratamento adequado de valores ausentes é fundamental para assegurar resultados confiáveis em análises futuras.


```python
# 3) Ok, no item anterior você deve ter achado pouco prático ler a informação de
# tantas variáveis, muitas delas nem devem ser interesantes. Então crie uma
# seleção dessa base somente com as colunas que interessam. São elas:

#  ['LOCNASC', 'IDADEMAE', 'ESTCIVMAE', 'ESCMAE', 'QTDFILVIVO', 'GESTACAO',
#   'GRAVIDEZ', 'CONSULTAS', 'APGAR5']

# Refaça a contagem de valores missings.

sinasc_interessa = sinasc[['LOCNASC',
                           'IDADEMAE',
                           'ESTCIVMAE',
                           'ESCMAE',
                           'QTDFILVIVO',
                           'GESTACAO',
                           'GRAVIDEZ',
                           'CONSULTAS',
                           'APGAR5']].copy()

print('Missing por variáveis: ')
print(sinasc_interessa.isna().sum())

print('\nContagem de valores missing: ')
sinasc_interessa.isna().sum().sum()
```

    Missing por variáveis: 
    LOCNASC          0
    IDADEMAE         0
    ESTCIVMAE      317
    ESCMAE         312
    QTDFILVIVO    1573
    GESTACAO      1232
    GRAVIDEZ        79
    CONSULTAS        0
    APGAR5         103
    dtype: int64
    
    Contagem de valores missing: 
    




    3616




```python
# 4) Apgar é uma nota que o pediatra dá ao bebê quando nasce de acordo com
# algumas características associadas principalmente à respiração.
# Apgar 1 e Apgar 5 são as notas 1 e 5 minutos do nascimento.
# Apgar5 será a nossa variável de interesse principal.
# Então remova todos os registros com Apgar5 não preenchido.
# Para esta seleção, conte novamente o número de linhas e o número de missings.

print('Qtd. total de linhas:')
print(len(sinasc_interessa))
print("Qtd. de dados faltantes na coluna 'APGAR5':")
print(sinasc_interessa['APGAR5'].isna().sum())
print()

sinasc_interessa.dropna(subset=['APGAR5'], inplace=True)

print('Qtd. de linhas após a remoção:')
print(len(sinasc_interessa))
print("Qtd. de dados faltantes na coluna 'APGAR5' após a remoção:")
print(sinasc_interessa['APGAR5'].isna().sum())
```

    Qtd. total de linhas:
    27028
    Qtd. de dados faltantes na coluna 'APGAR5':
    103
    
    Qtd. de linhas após a remoção:
    26925
    Qtd. de dados faltantes na coluna 'APGAR5' após a remoção:
    0
    


```python
# 5) Observe que as variáveis ['ESTCIVMAE', 'CONSULTAS'] possuem o código 9, que significa ignorado.
# Vamos assumir que o não preenchido é o mesmo que o código 9.

print('Quantidade de linhas com dados faltantes em cada variável:')
print(sinasc_interessa[['ESTCIVMAE', 'CONSULTAS']].isna().sum())

print('\nQuantidades total de linhas com dados faltantes:')
print(sinasc_interessa[['ESTCIVMAE', 'CONSULTAS']].isna().sum().sum())

print('\nDataFrame com as linhas que possuem dados faltantes:')
sinasc_interessa[sinasc_interessa[['ESTCIVMAE', 'CONSULTAS']].isna().any(axis=1)]
```

    Quantidade de linhas com dados faltantes em cada variável:
    ESTCIVMAE    315
    CONSULTAS      0
    dtype: int64
    
    Quantidades total de linhas com dados faltantes:
    315
    
    DataFrame com as linhas que possuem dados faltantes:
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>LOCNASC</th>
      <th>IDADEMAE</th>
      <th>ESTCIVMAE</th>
      <th>ESCMAE</th>
      <th>QTDFILVIVO</th>
      <th>GESTACAO</th>
      <th>GRAVIDEZ</th>
      <th>CONSULTAS</th>
      <th>APGAR5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>62</th>
      <td>1</td>
      <td>20</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>68</th>
      <td>1</td>
      <td>31</td>
      <td>NaN</td>
      <td>8 a 11 anos</td>
      <td>0.0</td>
      <td>37 a 41 semanas</td>
      <td>Dupla</td>
      <td>4</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>95</th>
      <td>1</td>
      <td>31</td>
      <td>NaN</td>
      <td>1 a 3 anos</td>
      <td>1.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>113</th>
      <td>1</td>
      <td>35</td>
      <td>NaN</td>
      <td>12 anos ou mais</td>
      <td>1.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>116</th>
      <td>1</td>
      <td>34</td>
      <td>NaN</td>
      <td>8 a 11 anos</td>
      <td>1.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>3</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>26585</th>
      <td>1</td>
      <td>22</td>
      <td>NaN</td>
      <td>8 a 11 anos</td>
      <td>0.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>26589</th>
      <td>1</td>
      <td>34</td>
      <td>NaN</td>
      <td>8 a 11 anos</td>
      <td>3.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>26911</th>
      <td>1</td>
      <td>33</td>
      <td>NaN</td>
      <td>4 a 7 anos</td>
      <td>7.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>26946</th>
      <td>1</td>
      <td>27</td>
      <td>NaN</td>
      <td>8 a 11 anos</td>
      <td>1.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>3</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>26953</th>
      <td>1</td>
      <td>23</td>
      <td>NaN</td>
      <td>4 a 7 anos</td>
      <td>2.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>3</td>
      <td>9.0</td>
    </tr>
  </tbody>
</table>
<p>315 rows × 9 columns</p>
</div>




```python
print('Contagem de dados faltantes por variável:')
print(sinasc_interessa.isna().sum())

sinasc_interessa.fillna(value={'ESTCIVMAE': 9,
                               'CONSULTAS': 9},
                        inplace=True)

print('\nRecontagem de dados faltantes por variável:')
print(sinasc_interessa.isna().sum())
```

    Contagem de dados faltantes por variável:
    LOCNASC          0
    IDADEMAE         0
    ESTCIVMAE      315
    ESCMAE         310
    QTDFILVIVO    1566
    GESTACAO      1216
    GRAVIDEZ        76
    CONSULTAS        0
    APGAR5           0
    dtype: int64
    
    Recontagem de dados faltantes por variável:
    LOCNASC          0
    IDADEMAE         0
    ESTCIVMAE        0
    ESCMAE         310
    QTDFILVIVO    1566
    GESTACAO      1216
    GRAVIDEZ        76
    CONSULTAS        0
    APGAR5           0
    dtype: int64
    


```python
# 6) Substitua os valores faltantes da quantitativa (QTDFILVIVO) por zero.

print("Quantidade de dados faltantes na coluna 'QTDFILVIVO':")
print(sinasc_interessa['QTDFILVIVO'].isna().sum())

sinasc_interessa['QTDFILVIVO'].fillna(value=0, inplace=True)

print("\nQuantidade de dados faltantes na coluna 'QTDFILVIVO' após a substituição:")
print(sinasc_interessa['QTDFILVIVO'].isna().sum())

```

    Quantidade de dados faltantes na coluna 'QTDFILVIVO':
    1566
    
    Quantidade de dados faltantes na coluna 'QTDFILVIVO' após a substituição:
    0
    


```python
# 7) Das restantes, decida que valore te parece mais adequado
#(um 'não preenchido' ou um valor 'mais provável' como no item anterior) e preencha.
#Justifique. Lembre-se de que tratamento de dados é trabalho do cientista,
#e que estamos tomando decisões a todo o momento - não há necessariamente certo e errado aqui.

print('Contagem de valores missing por variável:')
print(sinasc_interessa.isna().sum())

sinasc_interessa.fillna(value={'ESCMAE':9,
                               'GESTACAO': 9,
                               'GRAVIDEZ': 9},
                        inplace=True)

print('\nContagem de valores missing por variável após substituição:')
print(sinasc_interessa.isna().sum())

```

    Contagem de valores missing por variável:
    LOCNASC          0
    IDADEMAE         0
    ESTCIVMAE        0
    ESCMAE         310
    QTDFILVIVO       0
    GESTACAO      1216
    GRAVIDEZ        76
    CONSULTAS        0
    APGAR5           0
    dtype: int64
    
    Contagem de valores missing por variável após substituição:
    LOCNASC       0
    IDADEMAE      0
    ESTCIVMAE     0
    ESCMAE        0
    QTDFILVIVO    0
    GESTACAO      0
    GRAVIDEZ      0
    CONSULTAS     0
    APGAR5        0
    dtype: int64
    


```python
# 8)O Apgar possui uma classificação indicando se o bebê passou por asfixia:

# Entre 8 e 10 está em uma faixa 'normal'.
# Entre 6 e 7, significa que o recém-nascido passou por 'asfixia leve'.
# Entre 4 e 5 significa 'asfixia moderada'.
# Entre 0 e 3 significa 'asfixia severa'.

# Crie uma categorização dessa variável com essa codificação e calcule as frequências dessa categorização.

sinasc_interessa.loc[(sinasc_interessa['APGAR5'] >= 8) &
                     (sinasc_interessa['APGAR5'] <= 10),
                     'APGAR5_CLASSIFICADO'] = 'normal'

sinasc_interessa.loc[(sinasc_interessa['APGAR5'] >= 6) &
                     (sinasc_interessa['APGAR5'] <= 7),
                     'APGAR5_CLASSIFICADO'] = 'asfixia leve'

sinasc_interessa.loc[(sinasc_interessa['APGAR5'] >= 4) &
                     (sinasc_interessa['APGAR5'] <= 5),
                     'APGAR5_CLASSIFICADO'] = 'asfixia moderada'

sinasc_interessa.loc[sinasc_interessa['APGAR5'] <= 3,
                     'APGAR5_CLASSIFICADO'] = 'asfixia severa'

print('Frequencia de cada valor na categorização:')
print(sinasc_interessa['APGAR5_CLASSIFICADO'].value_counts())

print('\nFrequencia de cada valor na categorização em porcentagem:')
print(round(sinasc_interessa['APGAR5_CLASSIFICADO'].value_counts() / len(sinasc_interessa) * 100, 2))

```

    Frequencia de cada valor na categorização:
    normal              26463
    asfixia leve          320
    asfixia severa         74
    asfixia moderada       68
    Name: APGAR5_CLASSIFICADO, dtype: int64
    
    Frequencia de cada valor na categorização em porcentagem:
    normal              98.28
    asfixia leve         1.19
    asfixia severa       0.27
    asfixia moderada     0.25
    Name: APGAR5_CLASSIFICADO, dtype: float64
    


```python
# 9) Renomeie as variáveis para que fiquem no snake case, ou seja,
#    em letras minúsculas, com um underscore entre as palávras.
#    Dica: repare que se você não quiser criar um dataframe novo,
#    você vai precisar usar a opção inplace = True.

sinasc_interessa.rename(columns={'LOCNASC': 'local_de_nascimento',
                                 'IDADEMAE': 'idade_da_mae',
                                 'ESTCIVMAE': 'estado_civil_da_mae',
                                 'ESCMAE': 'escolaridade_da_mae__anos_de_estudo_concluídos',
                                 'QTDFILVIVO': 'quantidade_de_filhos_vivos',
                                 'GESTACAO': 'semanas_de_gestacao',
                                 'GRAVIDEZ': 'tipo_de_gravidez',
                                 'CONSULTAS': 'numero_de_consultas_de_prenatal',
                                 'APGAR5': 'apgar_no_quinto_minuto',
                                 'APGAR5_CLASSIFICADO': 'apgar5_classificado'},
                       inplace=True)
```


```python
sinasc_interessa
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>local_de_nascimento</th>
      <th>idade_da_mae</th>
      <th>estado_civil_da_mae</th>
      <th>escolaridade_da_mae__anos_de_estudo_concluídos</th>
      <th>quantidade_de_filhos_vivos</th>
      <th>semanas_de_gestacao</th>
      <th>tipo_de_gravidez</th>
      <th>numero_de_consultas_de_prenatal</th>
      <th>apgar_no_quinto_minuto</th>
      <th>apgar5_classificado</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>19</td>
      <td>5.0</td>
      <td>8 a 11 anos</td>
      <td>0.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>10.0</td>
      <td>normal</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>29</td>
      <td>2.0</td>
      <td>8 a 11 anos</td>
      <td>1.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>9.0</td>
      <td>normal</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>37</td>
      <td>9.0</td>
      <td>8 a 11 anos</td>
      <td>2.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>10.0</td>
      <td>normal</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>30</td>
      <td>5.0</td>
      <td>12 anos ou mais</td>
      <td>0.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>3</td>
      <td>10.0</td>
      <td>normal</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>30</td>
      <td>2.0</td>
      <td>8 a 11 anos</td>
      <td>1.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>10.0</td>
      <td>normal</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>27023</th>
      <td>1</td>
      <td>32</td>
      <td>2.0</td>
      <td>12 anos ou mais</td>
      <td>1.0</td>
      <td>32 a 36 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>9.0</td>
      <td>normal</td>
    </tr>
    <tr>
      <th>27024</th>
      <td>1</td>
      <td>19</td>
      <td>1.0</td>
      <td>8 a 11 anos</td>
      <td>0.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>9.0</td>
      <td>normal</td>
    </tr>
    <tr>
      <th>27025</th>
      <td>1</td>
      <td>24</td>
      <td>2.0</td>
      <td>8 a 11 anos</td>
      <td>0.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>10.0</td>
      <td>normal</td>
    </tr>
    <tr>
      <th>27026</th>
      <td>1</td>
      <td>21</td>
      <td>2.0</td>
      <td>8 a 11 anos</td>
      <td>1.0</td>
      <td>32 a 36 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>9.0</td>
      <td>normal</td>
    </tr>
    <tr>
      <th>27027</th>
      <td>1</td>
      <td>18</td>
      <td>5.0</td>
      <td>8 a 11 anos</td>
      <td>1.0</td>
      <td>37 a 41 semanas</td>
      <td>Única</td>
      <td>4</td>
      <td>8.0</td>
      <td>normal</td>
    </tr>
  </tbody>
</table>
<p>26925 rows × 10 columns</p>
</div>


