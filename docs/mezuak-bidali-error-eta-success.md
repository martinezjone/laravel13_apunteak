# MEZUAK BIDALI

Laravelek badu aukera bat ruta bati deitzerakoan mezuak bidaltzeko. Hau oso erabilgarria izaten da jakiteko ia guk esan dioguna bete duen ala ez.

Adibide batekin ikus dezakegu hau. Gure Aula ariketan, "Profesor" bat ezabatzerakoan ikusi behar dugu ia irakasle horrek ikastaroak dituen ala ez, zeren eta ikastaroak izatekotan ez diogu ezabatzen utziko.

Hori horrela izateko, lehenengo esta behin kontroladorean konprobaketa egingo dugu:

public function destroy(Profesor $profesor){

$cursosDelProfesor = $profesor->cursos;

// Ikastaroak egotekotan ezingo dugu ezabatu

if ($cursosDelProfesor->isNotEmpty()) {

return redirect()->route('profesores.index')

->with('error', 'Irakasleak ikastaroak ditu eta ezin da ezabatu.');

}

// Ikastarorik ez badago, irakaslea ezaba daiteke

$profesor->delete();

return redirect()->route('profesores.index')

->with('success', 'Irakaslea ongi ezabatu da');

}

Adibidean ikusten dugunez, with metodoari deituko diogu "error" edo "success" pasatzen.

Behin hori eginda, gure bistan jarriko dugu bata edo bestea idazteko, idatzi behar izanez gero:

@if(session('error'))

<div class="alert alert-danger">

{{ session('error') }}

</div>

@endif

@if(session('success'))

<div class="alert alert-success">

{{ session('success') }}

</div>

@endif

Ikusten dugunez, with metodoan saio baten gordetzen ditu error eta success aldagaiak. Kasu honetan hauek erabili ditu, eta hauek dira erabilienak, baina egia esan edozein aldagai pasatu ahal izango genioke.
