FROM ocaml/opam:debian-12-ocaml-5.2

ARG OCAML_VERSION=5.2.0
ARG ROCQ_CORE_VERSION=9.0.1
ARG ROCQ_STDLIB_VERSION=9.1.0
ARG STDPP_VERSION=1.13.0
ARG PACO_VERSION=4.2.3
ARG VSROCQ_LS_VERSION=2.4.3+1
ARG OPAM_JOBS=4

# ---- System Dependency ----
USER root
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      build-essential ca-certificates curl git m4 pkg-config \
      libgmp-dev rsync unzip less nano \
 && rm -rf /var/lib/apt/lists/*

USER opam
WORKDIR /home/opam

# ---- OCaml 5.2.0 switch ----
# Dockerfile에서는 eval $(opam env) 가 RUN 레이어를 넘어가면 사라지기 때문에,
# ENV OPAMSWITCH로 고정 
ENV OPAMYES=1
ENV OPAMJOBS=${OPAM_JOBS}
RUN opam switch create sf --packages=ocaml-base-compiler.${OCAML_VERSION} \
 && opam clean -a -c -s --logs
ENV OPAMSWITCH=sf

# ---- Rocq pkg repository ----
RUN opam repo add rocq-released https://rocq-prover.org/opam/released \
 && opam update

# ---- Versioning ----
# rocq-stdlib 9.1.0 은 "rocq-core >= 9.0" 이라 core 9.0.1 과 물리지만,
# 반면 rocq-stdlib 9.2.0 은 "rocq-core >= 9.1" 이므로 pin 을 풀면 깨져버림.
#
# rocq-prover 메타패키지는 core/stdlib 버전을 묶으므로 이 조합에서는 쓰지 않고 개별 패키지 설치 필요.
RUN opam pin add -n -y rocq-core   ${ROCQ_CORE_VERSION} \
 && opam pin add -n -y rocq-stdlib ${ROCQ_STDLIB_VERSION} \
 && opam pin add -n -y rocq-stdpp  ${STDPP_VERSION} \
 && opam pin add -n -y coq-paco    ${PACO_VERSION} \
 && opam install -y rocq-core rocq-stdlib rocq-stdpp coq-paco \
 && opam clean -a -c -s --logs

# ---- VsRocq 언어 서버 (VS Code 용) ----
# 이게 없으면 VS Code 에서 .v 파일을 열어도 골 창/스텝 진행이 동작하지 않음.
RUN opam install -y vsrocq-language-server.${VSROCQ_LS_VERSION} \
 && opam clean -a -c -s --logs

# ---- (option) coqc / coq_makefile binary ---
# Bryant의 Makefile 이 coqc, coq_makefile 를 부른다면, 주석 해제.
# RUN opam install -y coq-core.${ROCQ_CORE_VERSION} && opam clean -a -c -s --logs

# --- SHELL ---
ENV PATH=/home/opam/.opam/sf/bin:${PATH}
RUN echo 'eval $(opam env --switch=sf --set-switch)' >> /home/opam/.bashrc

WORKDIR /workspaces
CMD ["bash"]
