# CACHEKEY: "bionic_coq-V2019-11-05-V01"
# ^^ Update when modifying this file.

FROM ubuntu:bionic
LABEL maintainer="e@x80.org"

ENV DEBIAN_FRONTEND="noninteractive"

RUN apt-get update -qq && apt-get install --no-install-recommends -y -qq \
        # Dependencies of the image, the test-suite and external projects
        m4 automake autoconf time wget rsync git gcc-multilib build-essential unzip \
        # Dependencies of lablgtk (for CoqIDE)
        libgtksourceview-3.0-dev \
        # Dependencies of stdlib and sphinx doc
        texlive-latex-extra texlive-fonts-recommended texlive-xetex latexmk \
        xindy python3-pip python3-setuptools python3-pexpect python3-bs4 \
        # Dependencies of source-doc and coq-makefile
        texlive-science tipa

# More dependencies of the sphinx doc
RUN pip3 install sphinx==1.7.8 sphinx_rtd_theme==0.2.5b2 \
                 antlr4-python3-runtime==4.7.1 sphinxcontrib-bibtex==0.4.0

# We need to install OPAM 2.0 manually for now.
RUN wget https://github.com/ocaml/opam/releases/download/2.0.4/opam-2.0.4-x86_64-linux -O /usr/bin/opam && chmod 755 /usr/bin/opam

# Basic OPAM setup
ENV NJOBS="2" \
    OPAMJOBS="2" \
    OPAMROOT=/root/.opamcache \
    OPAMROOTISOK="true" \
    OPAMYES="true"

# Base opam is the set of base packages required by Coq
ENV COMPILER="4.05.0"

# Common OPAM packages.
# `num` does not have a version number as the right version to install varies
# with the compiler version.
ENV BASE_OPAM="num ocamlfind.1.8.1 dune.1.11.3 ounit.2.0.8 odoc.1.4.2" \
    CI_OPAM="menhir.20190626 ocamlgraph.1.8.8" \
    BASE_ONLY_OPAM="elpi.1.8.0"

# BASE switch; CI_OPAM contains Coq's CI dependencies.
ENV COQIDE_OPAM="cairo2.0.6.1 lablgtk3-sourceview3.3.0.beta6"

# Must add this to COQIDE_OPAM{,_EDGE} when we update the opam
# packages "lablgtk3-gtksourceview3"

# base switch
RUN opam init -a --disable-sandboxing --compiler="$COMPILER" default https://opam.ocaml.org && eval $(opam env) && opam update && \
    opam install $BASE_OPAM $COQIDE_OPAM $CI_OPAM $BASE_ONLY_OPAM

# base+32bit switch
RUN opam switch create "${COMPILER}+32bit" && eval $(opam env) && \
    opam install $BASE_OPAM

# EDGE switch
ENV COMPILER_EDGE="4.09.0" \
    COQIDE_OPAM_EDGE="cairo2.0.6.1 lablgtk3-sourceview3.3.0.beta6" \
    BASE_OPAM_EDGE="dune-release.1.3.2"

# EDGE+flambda switch, we install CI_OPAM as to be able to use
# `ci-template-flambda` with everything.
RUN opam switch create "${COMPILER_EDGE}+flambda" && eval $(opam env) && \
    opam install $BASE_OPAM $BASE_OPAM_EDGE $COQIDE_OPAM_EDGE $CI_OPAM

RUN opam clean -a -c
