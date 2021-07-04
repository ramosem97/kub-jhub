# Import Jupyter Lab Image
ARG BASE_CONTAINER=jupyter/all-spark-notebook:latest
FROM $BASE_CONTAINER

# name your environment and choose python 3.x version
ARG conda_env=base_env
ARG py_ver=3.9

# create Python 3.x environment and link it to jupyter
RUN conda create --quiet --yes -p "${CONDA_DIR}/envs/${conda_env}" python=${py_ver} ipython ipykernel && \
    conda clean -tipsy

RUN "${CONDA_DIR}/envs/${conda_env}/bin/python" -m ipykernel install --user --name="${conda_env}" && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

# Install from requirements.txt file
COPY --chown=${NB_UID}:${NB_GID} requirements.txt /env/
RUN "${CONDA_DIR}/envs/${conda_env}/bin/pip" install -r /env/requirements.txt

# Install Jupyter Lab Extensions
RUN "${CONDA_DIR}/envs/${conda_env}/bin/jupyter" labextension install @jupyter-widgets/jupyterlab-manager --no-build && \
    "${CONDA_DIR}/envs/${conda_env}/bin/jupyter" lab build

# prepend conda environment to path
ENV PATH "${CONDA_DIR}/envs/${conda_env}/bin:${PATH}"

# Entry Point
ENTRYPOINT ["jupyter", "lab", "--ip=0.0.0.0", "--allow-root"]
