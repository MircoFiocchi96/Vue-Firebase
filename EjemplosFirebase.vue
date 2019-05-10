<script>
//Primero anadir FIREBASE A TU APP WEB 

var database = firebase.database(); // asi le estoy diciendo que acceda a las cosas que tiene BD donde puedo leer, escribir, etc.(Tabmein esta autentificacion, storage, hosting, etc)


//Segunfo: GGUARDAR UNA REFERENCIA A LA BD
var db = firebase.database() // entonces, tengo disponible la BD en la variable db

//----------------------------------------------------------------------
//Tercero Guardar info de Perfil
//vamos a acceder a una ruta del arbl en nuestra bd 
//ACA SE SOBREESCRIBE LOS DATOS EN LA BD
db.ref('perfiles/mirco').set({ // en el set vamos a pasarle un objeto con la info que me interesa   
username: 'mirco', 
password: '123456',
ciudad:  'Cordoba',
}).then (() => console.info('Datos anadidos')) // esto se le llama promesa, me ingforma por consola qe fueron cargados los datos
//OJO PRIMERO QUE CAPAZ QUE NO HAYA AUTORIZACION PARA AGREGAR DATOS EN LA BD, HAY QUE HABILITARLA

//----------------------------------------------------------------------

//Agregar contenidos 
//en html LO LLAMO FORMULARIO
/* 
<div >
<form id="formularios" >
<textarea name="mensaje" id="mensaje" cols="30" row="10"> </textarea>
<br>
<input type="submit" value="Enviar Mensaje">
</form>
</div>
*/

//Entonces vamos a guardar esto en la BD en forma de lista
//capturamos el formulario que viene de html
//ACA NO SE SOBREESCRIBE LOS DATOS EN LA BD
document.getElementById('formulario').onsubmit=function(event){
    event.preventDefault()
    let mensaje = document.getElementById('mensaje')//capturar la informacion del mensaje que viene de html DONDE DICE id='mensaje'
    db.ref('chats').push({ //ahora tengo que 'empujarlo' hacia la BD
        username: 'mirco', 
        mensaje: mensaje.value
    }).then(()=> {
        console.info('Mensaje Enviado')
        mensaje.value= ''
    });
}
//------------------------------------------------------------------------------------------------------------------------------------------------------------------

// usar este libreria
<script src="https://cdnjs.cloudflare.com/ajax/vue/2.3.4/vue.js"></script> // usar este libreria

// vamos a usar vue stuff 
/* <div id="app">
<form @submit.prevent="enviarMensaje" >
<textarea v-model="mensaje" cols="30" row="10"> </textarea>
<br>
<input type="submit" value="Enviar Mensaje">
</form>
<hr>
<h1> Mensajes </h1>
<ul> //hago esto para mostrar por pantalla los datos que estan en Firebase es decir de la BD
    <li v-for="mensaje in mensajes ">
    {{mensaje.mensaje}}
    <small><i>{{mensaje.username}} </i> </small>
    </li>
</ul>
</div>
*/
new Vue({
    el: '#app',// asi lo menciono en html 
    created(){
        db.ref('/chats')
        .on('value ',snapshot => this.cargarMensajes (snapshot.val())) //captura de lo que hay en ese momento en la BD y el .val() es o que te muestra el valor de la BD
    }                                                                  // aca usamos el on que enviar varias veces, en cambio esta el ONCE que solamente se ejectua una vez
    data: {
        mensaje: null , 
        username: 'mirco',
        mensajes: [],
    }, 
    methods: {
        cargarMensajes(mensajes) {
            this.mensaje= [] //que se me limpie los mensajes locales para que no se me dupliquen 
            for(let key in mensajes ){
                this.mensaje.push({
                    mensaje: mensaje[key].mensaje, 
                    username: mensaje[key].username ,
                })
            }
                },
        enviarMensaje() {
            db.ref('/chats ')
            .push({
                mensaje: this.mensaje,
                username: this.username,
            });
         }
    },
    
})
//------------------------------------------------------------------------------------------------------------------------------------------------------------------
//Actualizar datos(registros) --> update()


// usar este libreria
<script src="https://cdnjs.cloudflare.com/ajax/vue/2.3.4/vue.js"></script> // usar este libreria

// vamos a usar vue stuff 
/* <div id="app">
<form @submit.prevent="enviarMensaje" >
<textarea v-model="mensaje" cols="30" row="10"> </textarea>
<br>
<input type="submit" value="Enviar Mensaje">
</form>
<hr>
<h1> Mensajes </h1>
<ul> //hago esto para mostrar por pantalla los datos que estan en Firebase es decir de la BD
    <li v-for="mensaje in mensajes ">
    <span contenteditable="true" @blur="editarMensaje($event,mensaje.key)>{{mensaje.mensaje}} </span>     // Para seleccionarlo y cambiarlo directamente, osea modificar.
                                                                                                          // el $EVENT manda lo que estas
    <small><i>{{mensaje.username}} </i> </small>
    <button @click="eliminarMensaje(mensaje.key)"> X </button>
    </li>
</ul>
</div>
*/
new Vue({
    el: '#app',// asi lo menciono en html 
    created(){
        db.ref('/chats')
        .on('value ',snapshot => this.cargarMensajes (snapshot.val())) //captura de lo que hay en ese momento en la BD y el .val() es o que te muestra el valor de la BD
    }                                                                  // aca usamos el on que enviar varias veces, en cambio esta el ONCE que solamente se ejectua una vez
    data: {
        mensaje: null , 
        username: 'mirco',
        mensajes: [],
    }, 
    methods: {
        cargarMensajes(mensajes) {
            this.mensaje= [] //que se me limpie los mensajes locales para que no se me dupliquen 
            for(let key in mensajes ){
                this.mensaje.push({
                    mensaje: mensaje[key].mensaje, 
                    username: mensaje[key].username ,
                    key:key,
                })
            }
                },
        enviarMensaje() {
            db.ref('/chats ')
            .push({
                mensaje: this.mensaje,
                username: this.username,
            });
         }
    },
    editarMensaje(mensaje,key){
        db.ref('/chats/' + key).update({
            mensaje: mensaje.target.innerHTML
        })

    },
    eliminarMensaje(key){
        if(confirm('Seguro?'))
            db.ref('/chats/' + key ).remove()

    }
    
})
</script>

<template>
<!-- HACER TRANSACCIONES-->
<!-- LO QUE VAMOS A REALIZAR AHORA ES TRANSACCIONES, CUANDO ALGUIEN QUIERE MODIFICAR ALGO DE LA BD Y HAY OTRO BOLUDO QUE TAMBIEN LA QUIERE CAMBIAR-->
    <div id="app">
        <h1> Datos del Usuario</h1>
        <dl>
            <dt><i>Username:</i></dt>
            <dd>{{datosPerfil.username}}</dd>
            <dt><i>Ciudad:</i></dt>
            <dd>{{datosPerfil.ciudad}}</dd>
            <dt><i>Pais:</i></dt>
            <dd>{{datosPerfil.pais}}</dd>
            <dt><i>Likes:</i></dt>
            <dd>{{datosPerfil.likes}}</dd>
            <button @click="actualizarLikes"> Anadir likes</button>
        </dl>

    </div>
</template>

<script>
    new vue({
        el: '#app',
        created(){
            db.ref('/perfiles').child('mirco')
            .on('value', snapshot => this.datoPerfil = snapshot.val() )
        },
        data: {
            datosPerfil:{ },

        },
        methods: {
            actualizarLikes(){
                db.ref('/perfiles').child('mirco').child('likes')

                .transaction(function(likesActuales){
                    return likesActuales + 1  //siempre va atener la ultima version o la ultima actulizacion
                }, function(error, commited, snapshot){
                    if (error) { //quiero saber si hay un error en la transaccion
                    console.error(error)
                    }else if (!commited){//sino se realizo
                    console.warn('Transaccion no realizada')
                    }else{  
                        console.info('Transaccion realizada ')
                        cosole.log(snapshot.val());
                    }
                })

            }
        },
    })

//---------------------------------------------------------------------------------------------------------------
//OTRO EJERCICIO MAS
//LISTA DE DATOS
new vue ({
    el: '#app',
    created(){
        const key = db.ref('chats').push().key;
        console.log(key)
        db.ref('chats').child(key).set({
            mensaje: 'Hola Mundo desde SET',
            username: 'mirco',
        });
        db.ref('/perfiles').child('mirco').child('chats').child(key).set(true);
        }
})
//-------------------------------------------------------------------------------------------------------------
//Eventos secundarios
new vue ({
    el: '#app',
    created(){
        // Dentro del console.log podemos poner data/data.val()/ data.key
        const charts = db.ref('chats')
        //child Added cuando se agrega un nodo (creo que aparece como booleano )
        chats.on('child_added', (data) => console.log(data.val())
        // child changed -> cuando modificio un subnodo me aparece. Te devuelvo el nuevo snapchto ya actulzado 
        chats.on('child_changed', (data) => console.log(data.val())
        //child removed -> me devuelve lo que elimine de la BD
        chats.on('child_removed', (data) => console.log(data.val())
        
    }
})

//-------------------------------------------------------------------------------------------------------------
// COMO ORDENAR LOS DATOS 

</script>
