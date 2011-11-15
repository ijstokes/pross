pross
====

### Overview


Pross is a python module including functions for the analysis of PDB files.  It includes utilities for calculating torsion angles, as well as methods for assigning secondary structures and mesostate codes. Originally Written by Pat Fleming, the Pross module was intended to reproduce functionality of the BIOMOL collection without the need to install the entire BIOMOL distribution.

### Download
[pross.py](pross.py) v463, July 9th, 2010

### Support
<a href="mailto:hpc-devel@sbgrid.org">hpc-devel@sbgrid.org</a>

### Examples

Below is an example of using the pross module to separate secondary structures from groups of residues:

    :::python
    import os.path
    import pross as p 

    def trimfile(in_fp, out_fp):
        if not os.path.exists(in_fp):
            return

        pdb = p.PDBFile(in_fp)
        chains = pdb.read(as_protein=1)

        for chain in chains.elements:
            (a, b, c, sst_list) = p.rc_ss(chain)

            idx = 0
            for res_class in sst_list:
                if res_class not in ["H", "E"]:
                   chain.elements[idx] = []
                idx += 1


        mol = p.molMol(chains=chains)
        p.write_pdb(mol, out_fp) # write modified PDB to disk


This function reads a pdb file and iterates through each of the residues and removes residues that are not in the 'H' (Helix) or 'E' (Strand) residue classes.

### Documentation
[PROSS Module API](PROSS.html)

Usage
-----

`pross.py pdbfile [oldmeso | fgmeso]`

Choose old mesostate definitions or finegrained mesostate definitions.
Default is finegrained.  



Imported Modules
----------------


Pross utilizes the following modules:

*   `sys`
*   `string`
*   `re`
*   `copy`
*   `gizp`
*   `math`
*   `types`  



Data Structures
---------------

_TypedList_

A Python list restricted to having objects of the same type.
An instance of a TypedList is created as follows:

`mylist = TypedList(function, [elements])`

where function is a python function which takes an argument and returns 1 or 0 indicating whether the object represented by the argument is of the correct type, and elements is an optional list of elements to be added into the instance.  Here is a full blown example.

    :::python
    def is_int(o):

        return type(o) == type(0)

    mylist = TypedList(is_int, [0, 1, 2, 3])

New elements are added to the list as follows:
`mylist.append(25)`

Instances of TypedList support all operations available for Python Lists (as of Python version 1.5.2a2)  




Major Functions
---------------
*   *res_rc*

    `res_rc(r1, r2, r3)` - Get mesostate code for a residue

    Given a phi (r1), psi (r2), and omega (r3) torsion angle, calculate the mesostate code that describes that residue. A mesostate will be returned in all but two cases:  if omega deviates from planarity by more than 90 degrees, '*' is returned.  Also, if any torsions are greater than 180.0 (biomol assignes 999.0 degrees to angles that that are indeterminate), prosst.INVALID is returned.  Here, r3 defaults to 180.0.  
  

*   *rc_codes*

    `rc_codes(chain, phi, psi, ome)` - return rotamer codes

    Given a protein chain (and optionally phi, psi, omega), this function will return a list of mesostate codes that applies to the chain, as determined by res_rc.  
      

*   *rc_ss*

    `rc_ss(chain, phi, psi, ome)` - calculate secondary structure

    This function calculates the secondary structure using the PROSS method with rotamer codes.  Given a chain, and optionally a list of phi, psi, and omega, it calculates the backbone secondary structure of the chain.  The return value is (phi, psi, ome, sst), where phi, psi, and ome are calculated if not specified, and sst is the secondary structure codes: H = helix, E = strand, P = PII, C = coil.  


*   *_rc_find*

    `_rc_find(codes, pat_obj)` - find a endpoints of a regexp

    Given a list of mesostate codes, this function identifies a endpoints of a match  <pattern>.  <pat_obj> is a compiled regular expression pattern whose matches will be returned as pairs indicated start, end in <codes> 

*   *write_pdb*
    
    `write_pdb` - write a PDB file from a molecular object

    write_pdb takes a biomol.Mol object and creates a PDB file based on the definitions given.  It can support all atomic properties as well as insertion codes and alternate locations. Essentially, biomol is designed such that if the original molecule has all that stuff it will be retained in the final PDB file.  If that is not desired, you must make the modifications to the Mol object yourself (or use one of the utilities).

    write_pdb takes the following arguments:

    *   myMol -- The **mol.Mol** object to be written.

    *   f --  A file instruction.  This can be a string, in which
               case f will be interpreted as a filename.  If the
               string f ends with .gz, the resulting file will be
               compressed using gzip.  Alternatively, f can be an
               open file object (it must have the write attribute).
               If f is an object, the PDB will be written to the
               object.  When f is a file object, SEQRES and END
               records will not be written by default.

    *   seq -- A boolean that determines whether SEQRES records
               will be written.  By default this is true (except in
               the case where f is an object). 
