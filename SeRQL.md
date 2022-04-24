---
layout: default
title: SeRQL
---

<div id="contact">
  <h1 class="pageTitle">SeRQL</h1>
  <p class="intro">
  SeRQL está desarrollado por la empresa Aduna, forma parte del software Sesame. Es un lenguaje de recuperación para RDF/RDFS que combina características de otros lenguajes(RQL, RDQL, N-Triples y N3).
  </p>
<p>
Su diseño surgió con el objetivo de combinar las ventajas de otros lenguajes, algunas características de este lenguaje son:
<ul>
  <li>Soporte de RDF Schema.</li>
  <li>Soporte en los tipos de datos de XML Schema.</li>
  <li>Transformación de grafos.</li>
  <li>Sintaxis expresiva para los path.</li>
  <li>Matching opcional de path.</li>
</ul>    
El lenguaje de recuperación SeRQL tiene tres principales componentes:
<ul>
  <li>URI's.</li>
  <li>Literales.</li>
  <li>Variables precedentes del lenguaje RDF.</li>
</ul>
En cuanto a la sintaxis, SeRQL es parecido a RQL, aunque añade algunas modificaciones que facilitan su análisis sintáctico, también llamado “parsing”, del lenguaje.
Al igual que en RQL SeRQL está basado en una interpretación del grafo de RDF.

Este lenguaje de recuperación es capaz de soportar expresiones generales como las de camino, emparejado de caminos opcionales, restricciones booleanas y dos iteradores básicos:
<ul>
  <li>select-from-where: Recupera las tablas junto a los valores y variables para los que hay una coincidencia con los datos.</li>
  <li>construct from where: Recupera un subgrafo con los datos del matching que se ha conseguido. Se permiten queries compuestas.</li>
</ul>
</p>