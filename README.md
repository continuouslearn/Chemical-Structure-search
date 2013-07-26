Chemical-Structure-search
=========================

Chemical Structure search capability using JSME 

1, Enable JS in wordpress
http://wordpress.org/plugins/allow-javascript-in-posts-and-pages/

cp allowJS.php wordpress/wp-content/plugins

2, Add these js into header.php file under folder wordpress/wp-content/themes/twentytwelve
$ vim header.php

<script src='http://chemmol.com/search/jsme/jsme.nocache.js'></script>
<script src='http://chemmol.com/js/jquery-2.0.3.min.js'></script>
<script src='http://chemmol.com/js/jquery.blockUI.js'></script>

3, Add post with code
[js]
function submitChemMolClientForm(exacts) {
  var mol = document.ChemMol_Structure.molFile();
  var smiles = document.ChemMol_Structure.smiles();
  var jme = document.ChemMol_Structure.jmeFile();

  if (smiles.length < 1)
  {
     alert("No molecule!");
  }
  else
  {
      $.ajax({
         type: "post",
         url: "http://chemmol.com/search/server.php",
         data: { a: 'n', jme: jme, mol: mol, smiles: smiles, ifexact: exacts },
         success: function(result) {
              $.unblockUI();
              $('#myresults').show()

              var presults = JSON.parse(result);

              if ( presults == 'No results' )
  	alert("No structures found!")
            
              var myr  = " ";
                  myr += " ";
                  myr += " ";
                  myr += " Product Information";
                  myr += "         Structure  ";
                  myr += "     ";
                  myr += "     ";

              $.each(presults.my_results, function(icount, compounds)
              {
                 myr += " ";
                 myr += "   Name:  "   + compounds.CName + " " ;
                 if ( compounds.CAS )
                 {
                    myr += "  CAS:  "   + compounds.CAS + " " ;
                 }
                 myr += "  Catalog ID:  "   + compounds.ucid + " " ;
                 myr += "  Supplier: <a href=\"" +compounds.cURL+  "\" target=new_chemmol>"   + compounds.company + "</a> ";
                 myr += "   " ;
                 myr += "    <img src=\"cmshowimage.php?id=" + compounds.mol_id+ "\" /> " ;           
              } );
              
              myr += "  ";

              if (presults.my_results.length == 10 )
              {
                myr += " [div id=\"show_more_results\" ] ";
                myr += " <input id=\"loading_button\" name=\"Show more results\" type=\"button\" border=1 value=\"Show more results\" ";
                myr += " onclick=\"show_more_results('" + presults.my_pointer + "')\"";
                myr += " [/div] " ;
              }


              $('#myresults').html(myr)
         },
         beforeSend: function() {
           // $('#loading').show();
            $.blockUI({ message: '<img src="/images_new/loading.gif" /> ' });
         },
         error: function (jqXHR, status) {
             alert('error')
         }
      });
  }
}
[/js]


[js]
    var jmeOnLoad=\[\];
    function jsmeOnLoad() {
        for (var i=0; i<jmeOnLoad.length; i++) {
                jmeOnLoad\[i\]();
        }
    }


jmeOnLoad.push(function() {
        document.ChemMol_Structure = new JSApplet.JSME("ChemMol_Structure", "720px", "350px", {
                "options" : "noquery,nohydrogens,polarnitro,nocanonize,oldlook"
        });
})
[/js]

<div id="ChemMol_Structure" align=center></div>

[js]
(
function() {
        var startingStructure=unescape(document.cookie.replace(/.*ChemMol_Structure=([^;]*).*|.*/,"$1"));
        if (startingStructure) {
                function loadMolecule() {
                        try {
                                        if (document.ChemMol_Structure.smiles().length==0) {
                                                        document.ChemMol_Structure.readMolecule(startingStructure);
                                return;
                                        }
                        } catch (error) {}
                        window.setTimeout(loadMolecule, 100);
                }
                window.setTimeout(loadMolecule, 100);
        }

        function saveMolecule() {
                        try {
                                var jmeFile=document.ChemMol_Structure.jmeFile();
                                if (jmeFile.length<1500) {
                                        if (startingStructure!=jmeFile) {
                                                document.cookie='ChemMol_Structure='+escape(jmeFile)+';expires='+new Date((new Date().getTime()) + 1000*60*60*24*365).toGMTString() +'; path=/; domain='+document.location.hostname.replace(/^.*(\..*\..*)$/,"$1");
                                                startingStructure=jmeFile;
                                        }
                                }
                        } catch (error) {}
        }

        window.setInterval(saveMolecule,500);

        }()
)
[/js]



    <input type="hidden" name="smiles">
    <input type="hidden" name="jme">
    <input type="hidden" name="mol">
    <input type="hidden" name="rinfo">
    <input type="hidden" name="exacts" value="n">

     <input class="btn btn-primary" type="button" value="Exact Search" onClick="submitChemMolClientForm('y')"  >      
     <input class="btn btn-primary" type="button" value="Substructure Search" onClick="submitChemMolClientForm('n')" >
     
</form>


                </div>

    <div id='myresults' style='display:none'>
    </div>

    <div id="loading"  style="display:none"></div>

		<div id="more_results">
				
		</div>
		
		<div id="page_loading" style="display:none" align="center" >
			<img src="../images_new/loading.gif" />	
		</div>
				
	</div>	


