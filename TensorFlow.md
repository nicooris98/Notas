# TensorFlow?

TensorFlow.js es una biblioteca de JavaScript para entrenar e implementar modelos de aprendizaje automático en el navegador web y en Node.js.

# Tensor

Un tensor es una matriz multidimensional.

```javascript
tf.tensor([1, 2, 3, 4])
```

## Caracterisiticas

- Dimensionalidad(Rank): Los tensores pueden tener diferentes dimensiones conocidas como rangos(0D, 1D, 2D, 3D, etc).
    
- Forma(Shape): Define el tamaño de cada dimensión del tensor. Por ejemplo, un tensor con forma (3, 2) representa una matriz con 3 filas y 2 columnas.
    
- Tipo de dato (dtype): Un tensor tiene un tipo de dato asociado, como tf.float32, tf.int32, etc.
    
- Variabilidad: Un tensor no puede variar/cambiar. Si necesitas que tu tensor llegue a cambiar entonces si te conviene trabajar con tensores variables. Para esto hay que usar `tf.variable`. Ej:
    
    ```javascript
    // tf.variable crea un tensor variable
    const x = tf.variable(tf.tensor([1, 2, 3]))
    x.print()//[1, 2, 3]
    // Aqui se le cambia el valor al tensor
    x.assign(tf.tensor([4, 5, 6]))
    x.print()//[4, 5, 6]
    ```
    

## Operaciones

Podemos multiplicar matrices igual que en algebra. Ej:
![](C:\Users\nicol\.config\joplin-desktop\resources\7623d2aca1bb49459ba08c26f2a4f49c.png)
```javascript
const a = tf.tensor2d([[1, 2, -3], [4, 0, -2]])
const b = tf.tensor2d([[3, 1], [2, 4], [-1, 5]])
tf.matMul(a, b).print()//[[10, -6], [14, -6]]
```
## Uso de memoria
Cada tensor ocupa memoria por lo que es necesario liberar esa memoria cuando ya no se usa el tensor con `tensor.dispose()`.
## Modelo
Un modelo es una funcion que calcula algo en los tensores. En TensorFlow, un modelo es una red neuronal con una o mas capas.
### Capas
Elementos donde se realizan operaciones específicas.
### Ejemplo
```javascript
// Se crea el modelo
const model = tf.sequential()
// Se crea la capa oculta
const hidden = tf.layers.dense({
units: 1,
inputShape: [1]
})
// Metemos la capa oculta en el modelo
model.add(hidden)
model.compile({
// Le asignamos la optimizacion
optimizer: "sgd",
// Asignamos la forma de reducir la perdida
loss: tf.losses.meanSquaredError
})

const x1 = tf.tensor1d([0, 5, 10, 15])
const x2 = x1.mul(1)// x2 = x1
model.fit(x1, x2, {shuffle: true, epochs: 500}).then(() => resFunction())
const resFunction = () => {
	for (let i = 0; i < 20; i++) {
	    const response = model.predict(tf.tensor1d([i]))
	    response.data().then(y => {
	        console.log(i + ": "+ y)
	    })
	}
}
```