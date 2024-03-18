# Aplicación para sumar y restar horas

## Descripción:

## Esta aplicación permite sumar y restar dos horas en formato HH:MM:SS utilizando solo Javascript.

## Tecnologías: 
- Javascript
- Quasar Framework
- Vue.js

## Funcionalidades: 
Sumar dos horas y obtener el resultado en formato HH:MM:SS.
Restar dos horas y obtener el resultado en formato HH:MM:SS.
Validar que las horas introducidas sean válidas.
Mostrar mensajes de error si las horas introducidas no son válidas.
Instrucciones de uso:

## Como instalar?
Instalar las dependencias: npm install
Iniciar la aplicación: npm run dev
Introducir las dos horas que desea sumar o restar en los campos correspondientes.
Hacer clic en el botón "Sumar" o "Restar".
El resultado se mostrará en el campo "Resultado".

## Ejemplos: 
Sumar 2 horas y 30 minutos a 10:00:00:
Introducir "10:00:00" en el campo "Hora 1".
Introducir "02:30:00" en el campo "Hora 2".
Hacer clic en el botón "Sumar".
El resultado se mostrará en el campo "Resultado": "12:30:00".
Restar 1 hora y 15 minutos a 14:00:00:
Introducir "14:00:00" en el campo "Hora 1".
Introducir "01:15:00" en el campo "Hora 2".
Hacer clic en el botón "Restar".
El resultado se mostrará en el campo "Resultado": "12:45:00".

## Explicación: 
La aplicación utiliza la clase Date de Javascript para convertir las horas introducidas en milisegundos. Luego, se suman o restan los milisegundos correspondientes a las dos horas y se convierte el resultado de nuevo a formato HH:MM:SS.
