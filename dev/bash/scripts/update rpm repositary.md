update rpm repositary
========================
```bash
#!/bin/bash
BASEDIR=/home/repo
BASEREPO=/repo/suse
PKGNAME=$1
REPO=$2

[ $# != 2 ] && echo "USE addpkg.sh <pkgname> local|test" && exit 2

case $2 in
        "local"|"test") echo "Updating $2 repository";;
        *)
            echo "Repository not found"
            exit 3
        ;;
esac

PKGFOUND=$(ls "$BASEDIR"/"$PKGNAME"*.rpm | wc -l)
if [ $PKGFOUND -gt 0 ]
then
        rpm --resign $BASEDIR/$PKGNAME*.rpm && echo "Package signed" 
        cp $BASEDIR/$PKGNAME* /repo/suse/$REPO && echo "Copied to repo"
        createrepo $BASEREPO/$REPO && echo "Repo updated"
        gpg --yes --detach-sign --armor $BASEREPO/$REPO/repodata/repomd.xml && echo "Repo signed"
else
        echo "Nothing to add. Check package name"
fi
```