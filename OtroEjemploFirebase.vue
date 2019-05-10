<template>
<head>
    <title>
        Vue
    </title>

    <div id="app">
        <button @click="ordenarPorKeySecundaria('color')">Por Color </button> <!--Le pasa color como paramentro en @click -->
        <button @click="ordenarPorKeySecundaria('edad')">Por Edad</button> <!--Le pasa edad como paramentro en @click -->
        <button @click="ordenarPorKey">Por key del nodo </button>
        <button @click="ordenarPorValor">Por valor</button>

    </div>
</head>   
</template>

<script src="https://cdnjs.cloudflare.com/ajax/vue/2.3.4/vue.js"></script> // usar este libreria
<script src="https://www.gstatic.com/firebasejs/4.1.5/firebase.js"></script>

<script>

//Inicialiar FIrebase 
const config = {
    apiKey:
    authDomain:
    databaseURl:
    projectId:
    storageBucket:
    messaingSenderId:
};

firebase.initializeApp(config)
const db= firebase.database()
const gatosRef= db.ref('gatos')

//Datos Iniciales
const gatos =[
    {nombre: 'Rouco ', edad:5, color: 'blanco y negro'},
    {nombre: 'Mora ', edad:3, color: 'azul'},
    {nombre: 'Giuli ', edad:2, color: 'violeta'},
    {nombre: 'Rosa ', color: 'naranja'}
]

new Vue ({
    el: '#app', 
    created(){
        gatos.forEach(gato=>{ //ACA ESTOR ANADIENDO LOS GATOS A FIREBASE !!!!!!!!!!!!!!!
            gatosRef.child(gato.nombre).set({
                edad: gato.edad || null, 
                color: gato.color
            });
        });
        // lo que me dice esto es avisarme si hubo algun cambio en ORDENAMIENTO de los datos        
        this.gatos.orderByChild('edad')
        .on('child_moved',snapshot =>{
            console.warn(snapshot.val());
        })
    },
    data: {
        gatos: gatosRef, //esta declarado mas arriba
    },
    methods: {
        ordenarPorKeySecundaria(key) {
            this.gatos.orderByChild(key)

// ACA ESTAN FILTAR DATOS !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

            .limitToFirst(2) //muestra los 3 primeros
            /*.limitToLast(2)*/ //Y este muestra los dos ultimos
            /*hAY otro que le puedo decir que muestre por donde yo le digo y que termine donde yo le digo */
            .startAt(1)
            .endAt(999)
            //Y por ultimo hay otro que se llama equalTo() que si por ej le ponemos edad=11 me va a traer todo lo que tenga ese numero
            .equalTo(11)
            .on('child_added', snapshot =>{
                console.log(snapshot.key, snapshot.val())
            })
        },
        ordenarPorKey(){
            this.gatos.orderByKey(key).on('child_added', snapshot =>{
                console.log(snapshot.key, snapshot.val())
            })            

        },
        ordenarPorValor(){
            db.ref('votos').orderByValue(key).on('child_added', snapshot =>{ //en la BD esta votos que tiene valores numericos
                console.log(snapshot.key, snapshot.val())
            })             
        }

    },
})
//----------------------------------------------------------------------------------------------------------------------------------
//ACA VAMOS VER COMO SE MANEJA LA SEGURIDAD EN BASE DE DATOS
/*
{
    "rules":{
        ".read": false,
        ".write": true, 
    }
}

*/
</script>
