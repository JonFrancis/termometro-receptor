# Termômetro sem fio – **RECEPTOR** (recebe e mostra a temperatura)

## {Configurar o rádio}
Na categoria ``||radio:rádio||``, arraste o bloco **``||radio:definir grupo do rádio||``** e troque o número para **o indicado pelo(a) professor(a)**.  
Esse número **deve ser igual** ao do emissor (para se "ouvirem").

```blocks
radio.setGroup(999)
```

## {Criar variáveis de estado}
Na categoria ``||variables:Variáveis||``, clique em **``||variables:Fazer uma variável||``** e crie:
- ``||variables:ultimaTemp||`` - A última temperatura mostrada  
- ``||variables:ultimoRxMs||`` - Variável para controlar se estamos recebendo algum sinal 

Depois, em ``||basic:Básico||`` pegue ``||basic:no iniciar||`` e **defina** as variáveis:

```blocks
let ultimaTemp = 0
let ultimoRxMs = 0
```

> Dica: **não vamos mostrar nada no display aqui**. Só guardaremos dados.

## {Receber valores pelo rádio}
Na categoria ``||radio:rádio||``, adicione o bloco **``||radio: ao receber rádio||``**.  
Dentro dele:
1. Em ``||logic:Lógica||`` use **``||logic:se <condição> então||``**.  
2. Arraste **``||logic:0 = 0||``** para a condição.  
3. Troque o primeiro **0** pela **variável name** e o segundo **0** por um texto **``T``** (clique e digite).  
4. Dentro do **se**, defina:
   - ``||variables:ultimaTemp||`` para **``valor``**  
   - ``||variables:ultimoRxMs||`` para **``||control:milissegundos desde que começou||``** (``||control:control||`` → ``millis``)

```blocks
radio.onReceivedValue(function (name, value) {
    if (name == "T") {
        ultimaTemp = value
        ultimoRxMs = control.millis()
    }
})
```

## {Criar uma função para mostrar a temperatura}
Abra ``||advanced:Avançado||`` → ``||functions:Funções||`` → **Criar função**.  
Nomeie **``mostrarTemp``** e crie **um parâmetro** chamado **``t``** (número).  

Dentro da função:
1. Em ``||text:Texto||``, use **``||text:unir||``** para formar **``(t arredondado) + "C"``**.  
   - Para arredondar, use ``||math:arredondar||`` (``||math:Matemática||``).  
2. Em ``||basic:Básico||``, use **``||basic:mostrar string||``** com o texto resultante.  
3. Adicione **``||basic:pausa (ms)||``** de **200**.  
4. Em seguida **``||basic:limpar tela||``**.

```blocks
function mostrarTemp (t: number) {
    basic.showString("" + Math.round(t) + "C")
    basic.pause(200)
    basic.clearScreen()
}
```

## {Mostrar tudo num único lugar}
Agora vamos ter **apenas um** bloco que controla o display para **evitar conflitos**.

Em ``||basic:Básico||`` arraste **``||basic:sempre||``** e dentro dele:
1. Crie a variável local **``agora``** = **``||control:millis||``**.  
2. Use um **``||logic:se … então … senão||``** para detectar **sem sinal**:
   - Condição: **``agora - ultimoRxMs > 5000``** (5s)  
3. **Se** estiver sem sinal:
   - Mostre **``||basic:ícone: Não||``**, pause 400 ms, limpe tela, pause 200 ms.  
4. **Senão**:
   - Chame **``mostrarTemp(ultimaTemp)``**.

```blocks
basic.forever(function () {
    let agora = control.millis()
    if (agora - ultimoRxMs > 5000) {
        basic.showIcon(IconNames.No)
        basic.pause(400)
        basic.clearScreen()
        basic.pause(200)
    } else {
        mostrarTemp(ultimaTemp)
    }
})
```

## {Teste}
- **Baixe** no micro:bit Receptor.  
- Sem o emissor ligado, você deverá ver o ícone "Sem sinal".  
- Quando o emissor começar a enviar, a temperatura **passa a aparecer** como "23C", "24C" etc.

## {Dica de ciência}
O micro:bit mede a temperatura **do próprio chip**. Se você segurar a placa, ela **aquece** e a leitura **sobe**; ao soltar, **demora um pouco** para esfriar.

```template
basic.forever(function () { })
```