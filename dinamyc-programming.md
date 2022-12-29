# Dynamic Programming & Algorithms

la progrmacion dinamica se trata de resolver en la menor cantidad de iteraciones un problema algoritmico, y se concentra en la visualizacion de los datos y del resultado esperado, una vez que se tiene la imagen de los datos, se analiza como conseguir el dato deseado y se busca la mejor manera de hacerlo

## memoization

es un proceso en el que guardamos valores unicos en un objeto y este evitara que se repitan calculaciones que ya hayan sido hechas por nuestro procedimiento, en este caso estamos calculando el numera 50 de la secuencia fibonacci con recursibidad, dicha funcion se llama a si misma 2 veces por cada interacion hasta llegar al caso base, donde sera devuelto 1 y se empezara a resolver la callStack, esto hace que nuestro valor de notacion O sean O elevado a la 50, lo que provocaria un numero enorme

al memoizar esta funcion los valores unicos seran guardados y en caso de repetirse seran devueltos por la funcion, evitando la recursion y cortando nuestro tiempo por mas de la mitad On

```js
const func = (n, memo = {}) => {
  // caso base que solo se ejecutara una vez alcanzado el objetivo
  if(n in memo) return memo[n]
  if(n <= 2) return 1
  memo[n] = func(n-1,memo) + func(n-2, memo)
  return memo[n]
};
console.log(func(50)); 


/* evaluar un arbol desde el caso base mas profundo

en este otro ejemplo tomaremos 2 parametros para determinar de cuantas formas yendo hacia abajo y hacia la derecha se puede llegar a un espacion especifico de una matriz */



const func = (gridX,gridY,memo = {}) => {
  //memoizacion, evaluamos si esta aplica al caso dado 
  let stringNumber = `${gridX},${gridY}`
  if(stringNumber in memo)return memo[stringNumber]

  //casos base, si se llega a la casilla 0, se salio de las dimensiones del arreglo y no es valido, si ambos coinciden en 1 significa que se llego a la misma casilla y por lo tanto aplica
  if(gridX === 1 && gridY === 1) return 1
  if(gridX === 0 || gridY === 0) return 0

  // cada valor es guardado en el memo para que en caso de darse de nuevo sera devuelto el resultado sin la necesidad de carcularlo de nuevo
  memo[stringNumber] = func(gridX-1,gridY, memo) + func(gridX,gridY-1, memo)
  return memo[stringNumber]
};
console.log(func(18,18));
//2333606220
```


## recipe para memoizacion de Alvin

### consigue que funcione (fuerza bruta)

1- visualiza el problema como un arbol: donde las raices llegan gasta el caso base y cada hoja es una llamada de la funcion con los parametros correctos

2- implementa el arbol usando recursion: una vez que se ve el problema y la forma en que se debe manipular los datos en forma de arbol es bastante mas sencillo aplicar la recursion

3- testealo: utiliza pruebas con valores pequeños para comprobar la correcta resolucion del problema

### hazlo eficiente (aplcia memoizacion)

1- crea un objeto para memoizar

2- hacer que los valores del caso base que se cumplan sean guardados en el memo antes de rotornar junto con el valor como identificador

3- guarda el elemento que cumpla las condiciones en el objeto memo

### pasos ejemplificados, esta funcion verifica si es posible conseguir un numero x de la suma de cualquier valor del arreglo Y

howsum
```js
const func = (tSum,numArr,memo = {}) => {
  
  /* casos base */
  //si la variable tsum Ya fue evaluada y existe en el objeto memo, no se ejecuta de nuevo en ninguna iteracion
  if(tSum in memo) return memo[tSum]
  //si se llega a 0. significa que fue posible una combinacion de numeros de numArr que al se sumados sea tSum
  if(tSum === 0) return []
  //en este caso la suma no es posible y se devuelve null
  if(tSum < 0) return null
  /* casos base */

  //se recorre el arreglo dado y por cada valor se crea una variable reminder que sera lo que quede de tSum - numArr[num]
  for(let num of numArr){
    const reminder = tSum - num
    //se usa recursion para evaluar los casos base con la variable nueva reminder como tSum
    const result = func(reminder,numArr, memo)
      if(result !== null){
      // si el valor es valido, se crea una entrada en el objeto memo para que no vuelva a ser evaluada
       memo[tSum] = [...result,num]
       return memo[tSum]
    }
  }

  //si se llega a esta linea, no fue posible encontrar un numero que cumpliera la condicion asi que el tSum se llena con null, no volvera a ser evalaudo y se devuelve
  memo[tSum] = null
  return memo[tSum]

};
console.log(func(300, [7,14]));
//null
```
cansum
```js
const func = (x,y, memo = {}) => {
  if(x in memo) return memo[x]
  if(x === 0) return true
  if(x < 0) return false

  for(let num of y){
    const reminder = x - num
    if(func(reminder,y, memo) === true){
      memo[x] = true
      return true
    }
  }
  memo[x] = false
  return false
};
console.log(func(300, [7,14]));
//false
```
bestsum
```js
const bestSum = (targetSum, numbers, memo = {}) => {
  if(targetSum in memo) return memo[targetSum]
  if(targetSum === 0) return []
  if(targetSum < 0) return null

  let shortestCombination = null

  for(let num of numbers){
    const remainder = targetSum - num
    const remainderCombination = bestSum(remainder, numbers, memo)
    if(remainderCombination !== null){
      const combination = [...remainderCombination, num ]
      if(shortestCombination === null || shortestCombination.length > combination.length){
        shortestCombination = combination
      }
    }
  }
  memo[targetSum] = shortestCombination
  return memo[targetSum]
};
console.log(bestSum(100, [1,2,5,25]));
```


"construc the word with the give array"
```js
const func = (target,cancon,memo={}) => {
  if(target in memo) return memo[target]
  if(target === '')return true

 for(let group of cancon){
  // en este if se evalua si el grupo se encuentra al principio del target, un string dado a indexOf evaluara si dicho string contiene el valor dado al metodo o delvolvera -1
   if(target.indexOf(group) === 0){
      const answer = func(target.slice(group.length),cancon,memo)
      if(answer){
        memo[target]=true
        return memo[target]
      }
    }
  }

 memo[target] = false
 return memo[target]
};
console.log(func('abcdef', ['ab','abc','cd','def','abcd']));
```

"all the posible ways to construc a target word with the given array"
```js
 const allConstruc = (target,cancon,memo={}) => {
    if(target in memo) return memo[target]
    if(target === '')return [[]]
    let result = []
  
   for(let word of cancon){
      if(target.indexOf(word) === 0){
        const suffix = target.slice(word.length)
        const allSuffixWays = allConstruc(suffix,cancon,memo)
        const targetWays = allSuffixWays.map(way => [word, ...way])
        result.push(...targetWays)
      }
    }
  
   memo[target] = result
   return result
  };
  
  console.log(allConstruc('abcdef', ['ab','abc','cd','def','abcd']));
```
