In jenkins file
        1 / for using multi nodes with tags we use agent {label 'tag'} and for global pipeline agent none
        2/ remarque :
                chaque execution de stage jenkins refait le pull ce qui fait le changement si c'est fait par le pipeline va etre ecraser,
                ce qui fait on utilise le checkout avec stash.
                
        
