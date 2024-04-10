# Practica de Agregaciones

1. Cuenta los productos de tipo “medio”, usando un método básico

```json
[
  {
    "$match":
      {
        "tipo": "medio",
      },
  },
  {
    "$count":
      "total medios",
  },
]
```

2. Indicar con un distinct, las empresas (fabricantes) que hay en la colección

```json
[
  {
    "$group":
      {
       " _id": "$fabricante",
      },
  },
  {
    "$project":
      {
       " _id": 1,
      },
  },
]
```

3. Usando aggregate, visualizar los productos que tengan más de 80 unidades

```json
[
  {
    "$match":
      {
        "unidades": {
          "$gt": 80,
        },
      },
  },
]
```

4. Con $project visualizar solo el nombre, unidades y precio de los productos que tengan menos de 10 unidades

```json
[
  {
    "$match": {
      "unidades": {
        "$lt": 10,
      },
    },
  },
  {
    "$project": {
     " _id": 0,
      "nombre": 1,
      "unidades": 1,
      "precio": 1,
    },
  },
]
```

5. Con $project ponemos el fabricante, pero le cambiamos el nombre por “empresa”.Usamos el mismo comando anterior

```json
[
  {
    "$match": {
      "unidades": { 
        "$lt": 10 
      }
    }
  },
  {
    "$project": {
      "_id": 0,
      "nombre": 1,
      "unidades": 1,
      "precio": 1,
      "empresa": "$fabricante"
    }
  }
]

```

6. Añadir a la consulta anterior un campo calculado que se llame total y que multiplique precio por unidades.

```json
[
  {
    "$match": {
      "unidades": { 
        "$lt": 10 
      }
    }
  },
  {
    "$addFields": {
      "total": { 
        "$multiply": ["$precio", "$unidades"] 
      }
    }
  },
  {
    "$project": {
      "_id": 0,
      "nombre": 1,
      "unidades": 1,
      "precio": 1,
      "empresa": "$fabricante",
      "total": 1
    }
  }
]

```

7. Hacer que el nombre salga en mayúsculas con el operador $toUpper

```json
[
  {
    "$match": {
      "unidades": {
         "$lt": 10 
      }
    }
  },
  {
    "$addFields": {
      "total": { 
        "$multiply": ["$precio", "$unidades"] 
      }
    }
  },
  {
    "$project": {
      "_id": 0,
      "nombre": { 
        "$toUpper": "$nombre" 
      },
      "unidades": 1,
      "precio": 1,
      "empresa": "$fabricante",
      "total": 1
    }
  }
]

```

8. Añadir un campo calculado que ponga el nombre del producto y el tipo concatenado con el operador $concat. Le llamamos al campo “completo”

```json
[
  {
    "$match": {
      "unidades": {
         "$lt": 10 
         }
    }
  },
  {
    "$addFields": {
      "total": { 
        "$multiply": ["$precio", "$unidades"] 
        }
    }
  },
  {
    "$addFields": {
      "completo": { 
        "$concat": ["$nombre", " - ", "$tipo"]
         }
    }
  },
  {
    "$project": {
      "_id": 0,
      "nombre": { 
        "$toUpper": "$nombre" 
        },
      "unidades": 1,
      "precio": 1,
      "empresa": "$fabricante",
      "total": 1,
      "completo": 1
    }
  }
]

```

9. Ordena el resultado por el campo “total”

```json
[
  {
    "$match": {
      "unidades": {
         "$lt": 10 
         }
    }
  },
  {
    "$addFields": {
      "total": { 
        "$multiply": ["$precio", "$unidades"] 
        }
    }
  },
  {
    "$addFields": {
      "completo": { 
        "$concat": ["$nombre", " - ", "$tipo"]
         }
    }
  },
  {
    "$project": {
      "_id": 0,
      "nombre": { 
        "$toUpper": "$nombre" 
        },
      "unidades": 1,
      "precio": 1,
      "empresa": "$fabricante",
      "total": 1,
      "completo": 1
    }
  },
  {
    "$sort": {
      "total": 1
    }
  }
]

```

10. Haciendo una nueva consulta, averiguar el numero de productos por tipo de producto

```json
[
  {
    "$group": {
      "_id": "$tipo",
      "total_productos": { 
        "$sum": 1
      }
    }
  }
]

```

11. Añadir el valor mayor y el menor

```json
[
  {
    "$group": {
      "_id": "$tipo",
      "total_productos": { 
        "$sum": 1 
        },
      "valor_mayor": {
         "$max": "$precio" 
        },
      "valor_menor": { 
        "$min": "$precio"
        }
    }
  }
]

```

12. Añade el total de unidades por cada tipo

```json
[
  {
    "$group": {
      "_id": "$tipo",
      "total_productos": { 
        "$sum": 1 
        },
      "total_unidades": { 
        "$sum": "$unidades"
        },
      "valor_mayor": { 
        "$max": "$precio" 
        },
      "valor_menor": {
         "$min": "$precio" 
       }
    }
  }
]

```

13. Con el operador $set y el operador “$substr” visualiza todos los datos del producto "Small Metal Tuna" y los primeros 5 caracteres del nombre

```json
[
  {
    "$match": {
      "nombre": "Small Metal Tuna"
    }
  },
  {
    "$set": {
      "primeros_caracteres": { 
        "$substr": ["$nombre", 0, 5] 
      }
    }
  }
]

```

14. Creamos una salida que tenga el nombre del articulo y el total (precio por unidades) y lo guardamos en una colección denominada productos2

```json
[
  {
    "$project": {
      "_id": 0,
      "nombre": 1,
      "total": { 
        "$multiply": ["$precio", "$unidades"]
      }
    }
  },
  {
    "$out": "productos2"
  }
]

```