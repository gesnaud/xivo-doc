XiVO-doc
========

This is the documentation for the [XiVO project](http://xivo.io "XiVO homepage").

The live version is hosted at http://documentation.xivo.io


Dependencies
------------

* Sphinx (package python-sphinx on Debian)
* sphinx-git modified (for git changelog). To install it:

   pushd /tmp && sudo pip install git+git://github.com/xivo-pbx/sphinx-git.git@tagtitles && popd


Build
-----

   make html


PDF version
-----------

You will need a LATEX compilation suite. On Debian, you can use the following
packages :

$ apt-get install texlive-latex-base texlive-latex-recommended
texlive-latex-extra texlive-fonts-recommended
