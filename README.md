1. Utilizați pentru texturare imagini cu transparență și fără. Ce observați?

    Imagini fără transparență (de ex., format JPG sau PNG fără canal alpha): Acoperă complet zona texturată a obiectului. Fiecare pixel al texturii este opac, înlocuind sau amestecându-se cu culoarea de bază a obiectului.

    Imagini cu transparență (de ex., PNG cu canal alpha): Permit să se vadă prin anumite zone ale obiectului. Partile unde canalul alpha are valoarea 0 sunt complet transparente, iar cele cu valori intermediare (între 0 și 1) sunt translucide.

    Ce se observă: Pentru a folosi corect transparența în OpenGL, este necesar să se activeze amestecarea (blending) cu funcția glEnable(GL_BLEND) și să se definească o funcție de amestec (de ex., glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA)). Fără această activare, pixelii transparenți vor fi tratați ca având alpha=1 (opaci) și vor afișa, de obicei, o culoare de fundal nedorită (ex. negru sau alb). Ordonarea desenării obiectelor transparente devine critică (se desenează de la distanțe mari spre cele mici) pentru a evita artefacte.

2. Ce formate de imagine pot fi aplicate în procesul de texturare în OpenGL?

OpenGL suportă o gamă largă de formate de imagine prin biblioteci auxiliare (de încărcare) precum stb_image.h. Formatele comune includ:

    Format comune pentru încărcare: JPEG (JPG), PNG, BMP, TGA, GIF, PSD.

    Format cu caracteristici avansate (recomandate pentru texturare):

        PNG: Suportă transparență (canal alpha) și compresie fără pierderi. Cel mai folosit pentru texturi generale.

        JPEG: Compresie cu pierderi, fără transparență. Bun pentru texturi de fundal sau unde calitatea nu este critică.

        TGA (Targa): Un format mai vechi, dar bine acceptat, suportă canal alpha.

        BMP: Necomprimat, fără transparență, ușor de încărcat dar cu fișiere mari.

    Format profesionale/GPU-friendly (utilizate în producție): După încărcare, datele texturii sunt stocate în memorie video într-un format intern OpenGL. Aceste formate sunt definite de măști de biți și canale (de ex., GL_RGB, GL_RGBA). Există și formate comprimate pe GPU, precum DDS sau KTX, care pot stoca texturi într-o formă direct accesibilă de către GPU, economisind memorie și timp de încărcare. Acestea necesită încărcători specializați.

3. Specificați ce se întâmplă atunci când se modifică culoarea (prin manipularea canalelor RGB) obiectului texturat.

Modificarea culorii obiectului texturat se face de obicei prin multiplicarea (modularea) culorii vertexurilor (glColor3f în versiuni vechi sau variabile out în shadere) cu culoarea texturii.

    Efect: Culorile din textură sunt scalate (înmulțite) cu valorile RGB ale culorii setate pe obiect. De exemplu:

        Culoare albă (1.0, 1.0, 1.0) lasă textura neschimbată.

        Culoare gri (0.5, 0.5, 0.5) întunecă textura.

        Culoare roșie (1.0, 0.0, 0.0) păstrează doar canalul roșu al texturii, dând o nuanță roșiatică întregii texturi.

    Utilizare: Această tehnică este foarte utilă pentru a atinge (tint) o textură, pentru a crea efecte de iluminare (culorile vertexurilor pot fi modulate de iluminare) sau pentru a face tranziții de culoare fără a încărca texturi noi.

4. Ce deosebiri există între scena ce utilizează obiecte texturate în modul iluminare activat, respectiv dezactivat?

    Iluminare DEZACTIVATĂ (glDisable(GL_LIGHTING)): Textura este aplicată direct, iar culoarea finală a pixelului este determinată în principal de culoarea texturii și eventual de culoarea vertexului (dacă se modulează). Obiectul va arăta plat și uniform luminat, indiferent de poziția surselor de lumină, ca și cum ar fi autoluminiscent sau iluminat global uniform. Detaliile de relief ale texturii sunt vizibile doar prin varietatea culorilor, nu prin umbre sau reflexii.

    Iluminare ACTIVATĂ (glEnable(GL_LIGHTING)): Culoarea texturii este combinată cu calculele de iluminare (difuză, speculară, ambientală). Efectul este mult mai realist:

        Suprafața obiectului va avea zone mai luminoase unde este perpendiculară pe direcția luminii și zone întunecate în umbră.

        Se pot observa evidențieri speculare pe suprafețe lucioase.

        Relieful sugerat de textură (ex.: o textură de cărămidă) va părea mai credibil, deoarece iluminarea accentuează impresia de volum, chiar dacă geometria este plană (efect numit "iluminare per-vertex").

        Modul de texturare: În iluminare clasică (fixed-pipeline), se folosește de obicei GL_MODULATE, care înmulțește culoarea texturii cu culoarea calculată de iluminare. În shadere moderne, acest proces este controlat complet de programator, permițând modele de iluminare și texturare foarte complexe (ex.: mapare de normals - bump mapping).
