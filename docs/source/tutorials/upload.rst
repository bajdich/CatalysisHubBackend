Submitting data
---------------

Please submit your electronic structures calculations for surface reactions! Data submissions will be part of the Surface Reactions app at http://www.catalysis-hub.org/energies (essentially CatApp v2.0) and is open to all institutions. Furthermore, the atomic structures that are part of your reaction can be utilized for other apps.

Your publication/dataset will be listed on the http://www.catalysis-hub.org/publications page, with a link to the publishers homepage (if already published). Your data will be easily accessible to other researchers, who will be able to browse through reaction energies and atomic structures from your publication.

SUNCAT group members
....................
If you're a member of the SUNCAT group, you can add your data to one of the folders on the Sherlock or SUNCAT cluster:

* SHERLOCK2 : /home/users/winther/data_catapp/
* SUNCAT : /nfs/slac/g/suncatfs/data_catapp/

Start by activating the corresponding virtualenv. On SHERLOCK2 ::

  . /home/users/winther/data_catapp/CATKITENV/bin/activate

or on SUNCAT ::

  . /nfs/slac/g/suncatfs/data_catapp/CATKITENV/bin/activate



to use the local installation of CatKit. You should see a `(CATKITENV)` at the beginning of your prompt now to indicate that all python script and libraries are first imported from the virtualenv. To return your shell to the previous state simply type::

  deactivate

or log out. Now you can go straight to `cathub organize`_ .

Alternatively you can install your own version of CatKit - see instructions below.


Installing CatKit
...........................
CatKit is set of computational tools for catalysis, that comes with a cli tool ``cathub`` that will be used to arrange your data into folders and submit your data to the server. To install CatKit, together with the ASE dependency, use pip::

  pip install --upgrade --user git+https://gitlab.com/ase/ase.git@database_lesstables#egg=ase-3.16.3b1
  pip install --upgrade --user git+https://github.com/kirstenwinther/CatKit.git#egg=catkit

which will install a developer version of ASE with an enhanced database module, CatKit and all their dependencies.

To test that the cathub cli is working, start by typing::

  cathub

And you should see a list of subcommands. If it's not working you probably have to add the installation path to :code:`PATH` in your ~/.bashrc. This would typically be :code:`export PATH=~/.local/bin:${PATH}` for Linux, and :code:`export PATH~/Library/PythonX.Y/bin:${PATH}` for Mac.

Organizing data
....................
You have two options for organizing your data:

* cathub organize: For larger systematic datasets without reaction barriers, this approach will create folders and and arrange your data-files in the right location for you.

* cathub make_folders: For smaller or more complicated datasets with reaction barriers, this method will only create your folders, and you will have to drop the files in the right location yourself.

cathub organize
................
This tool will take all your structure files from a general folder and organize them in the right folder-structure that can be used for data submission. Note: this approach does not work for transition states / barrier calculations. And it will still need a lot of manual file organization for co-adsorbate configurations.  While we are working on this cathub organize might still give you a nice head start with file organization.

To learn about the organize command, type::

  cathub organize --help

To read the data from a general folder, type::

  cathub organize <FOLDER> -a ADS1,ADS2 -c <dft-code> -x <xc-functional> -f <facet> -S <crystal structure>

Use the ``-a`` option to specify which adsorbates to look for. Also, please use the ``-c`` and ``-x`` options to specify the DFT code and xc-functional respectively. Furthermore, you are highly encurraged to use the ``-f`` and ``-S`` options to specify the surface facet and crystal structure when applicable.

This will generate an organized folder named ``<FOLDER>.organized``. Please open the .txt file ``<FOLDER>.organized/publication.txt``, and update it with info about your publication. It should look something like this::

    volume: 8
    publisher: Wiley
    doi: 10.1002/cssc.201500322
    title: "The Challenge of Electrochemical Ammonia Synthesis: A New Perspective on the Role of Nitrogen Scaling Relations"
    journal: ChemSusChem
    authors: [Montoya, Joseph H., Tsai, Charlie, Vojvodic, Aleksandra, Norskov, Jens K.]
    year: 2015
    number: 13
    tags: []
    pages: 2140-2267

 
Note that authors should be a list, with names in the form "lastname, firstname M.".

Please go through the created folder and rename folders to make your data easier to localize later. For example, a structure folder like Pt16_structure, could be changed to Pt16_fcc or Pt16_bcc respectively. Please do not use spaces in folders or file-names.

If you, for example, have calculations with different facets, you can also split them into separate folders, run ``cathub organize -f <facet>``, and them merge the organized folders together afterwards with :code:`cp -R organized1 organized2`.


cathub make_folders
...................
This tool will create the right folder structure for you, but you must dump your files yourself.

To learn about the make_folders command type::

  cathub make_folders --help

Then create a folder in your user-name, 'cd' into it and type::

  cathub make_folders --create-template <TEMPLATE>

This will create a template (txt) file, that you should update with your publication and reaction info. The template should look similar to this::

    title: "The Challenge of Electrochemical Ammonia Synthesis: A New Perspective on the Role of Nitrogen Scaling Relations"
    authors: [Montoya, Joseph H., Tsai, Charlie, Vojvodic, Aleksandra, Norskov, Jens K.]
    journal: ChemSusChem
    volume: 8
    number: 13
    pages: 2140-2267
    year: 2015
    publisher: Wiley
    doi: 10.1002/cssc.201500322
    DFT_code: Quantum Espresso
    DFT_functionals: [BEEF-vdW]
    reactions:
    -   reactants: [0.5H2gas, star]
	products: [Hstar@bridge]
    -   reactants: [0.5H2gas, star]
	products: [Hstar@fcc]
    -   reactants: [0.5H2gas, star]
	products: [Hstar@hollow]
    -   reactants: [0.5H2gas, star]
	products: [Hstar@ontop]
    -   Reactants: [0.5N2gas, 0.5H2gas, star]
	products: [NHstar@bridge]
    -   reactants: [0.5N2gas, 0.5H2gas, star]
	products: [NHstar@hollow]
    -   reactants: [0.5N2gas, star]
	products: [Nstar@hollow]
    bulk_compositions: [Co]
    crystal_structures: [fcc]
    facets: ['111']

Consult :code:`cathub make_folders --help` again for detailed instructions on how to specify the types of reactions and surfaces.

Then type::

   cathub make_folders <TEMPLATE>

And your folders will be created. You can check that they look right with :code:`tree -F <FOLDER>`. The template above will produce the following folder tree::

  $tree -F MontoyaChallenge2015/

  MontoyaChallenge2015
  ├── Quantum\ Espresso/
  │   └── BEEF-vdW/
  │       ├── Co_fcc/
  │       │   ├── 111/
  │       │   │   ├── 0.5H2gas_star__Hstar@bridge/
  │       │   │   │   ├── MISSING:H_slab
  │       │   │   │   └── MISSING:TS?
  │       │   │   ├── 0.5H2gas_star__Hstar@fcc/
  │       │   │   │   ├── MISSING:H_slab
  │       │   │   │   └── MISSING:TS?
  │       │   │   ├── 0.5H2gas_star__Hstar@hollow/
  │       │   │   │   ├── MISSING:H_slab
  │       │   │   │   └── MISSING:TS?
  │       │   │   ├── 0.5H2gas_star__Hstar@ontop/
  │       │   │   │   ├── MISSING:H_slab
  │       │   │   │   └── MISSING:TS?
  │       │   │   ├── 0.5N2gas_0.5H2gas_star__NHstar@bridge/
  │       │   │   │   ├── MISSING:NH_slab
  │       │   │   │   └── MISSING:TS?
  │       │   │   ├── 0.5N2gas_0.5H2gas_star__NHstar@hollow/
  │       │   │   │   ├── MISSING:NH_slab
  │       │   │   │   └── MISSING:TS?
  │       │   │   ├── 0.5N2gas_star__Nstar@hollow/
  │       │   │   │   ├── MISSING:N_slab
  │       │   │   │   └── MISSING:TS?
  │       │   │   └── MISSING:empty_slab
  │       │   └── MISSING:Co_fcc_bulk
  │       └── gas/
  │           ├── MISSING:H2_gas
  │           └── MISSING:N2_gas
  └── publication.txt


Then add your atomic structure output files to the right folders. The files can be in any format that ASE can read, and must contain the total potential energy from the calculation. ASE trajectory (.traj) files are generally preferred. If you're using Vasp, please add your OUTCAR files as ``<name>.OUTCAR``. Your structures will include the adsorbed atoms/molecules, empty slabs, and gas phase species for your reactions. Also, if you have done calculations for the bulk geometries, please include them as well. All gas phase species involved must be added to the ``<publication>/<dft code>/<dft functional>/gas/`` folder. Also, notice that dummy files named ``MISSING:..`` have been placed in the folders, to help you determine the right location for your files. It's recommended to write a script to transfer files from one folder-structure to another in a systematic way, for example using :code:`shutils.copyfile('/path/to/initial/file', '/path/to/final/file')`.

Reading into database
......................
After adding all your structure files (or after running cathub organize), read your structures into a local database file with the command::

  cathub folder2db <FOLDER> --userhandle <slack-username or gmail-address>

Remember your ``userhandle`` since it will be used to log in at http://www.catalysis-hub.org/upload later (to be implemented).

If anything is wrong with your files, or anything is missing, you should receive appropriate error messages. When reading of the folder is complete, a table with a summary with reaction energies will be printed in you terminal. Please verify that everything looks right. Also, a database file has been written at ``<FOLDER>/<DBNAME>.db``.

Upload your data to the server by typing::

  cathub db2server <DBNAME>.db

and follow the feedback in the terminal. Your data will not be made accessible from catalysis-hub.org before you have approved. Send an email to Kirsten Winther,  winther@stanford.edu, and request to have your data made public. Please include the ``userhandle`` you defined above in the email.
