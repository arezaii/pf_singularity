Bootstrap: docker
From: centos:7.4.1708
%labels
    # Modified from Dockerfile written and maintained by Steven Smith <smith84@llnl.gov>
%post
    #RMM ParFlow test
    
    #-----------------------------------------------------------------------------
    # start by building the basic container
    #-----------------------------------------------------------------------------
    
    #-----------------------------------------------------------------------------
    #  Package dependencies
    #-----------------------------------------------------------------------------
    yum -y  install  epel-release
    yum  install -y  \
	atk \
	autoconf \
	automake \
	binutils \
	cairo \
	cmake3 \
	createrepo \
	ethtool \
	file \
    gcc  \
    gcc-c++  \
    gcc-gfortran \
    git \
	gtk2 \
    hdf5-devel \
    hdf5-openmpi \
    hdf5-openmpi-devel \
    hdf5 \
	libtool \
	lsof \
    make \
	python-devel \
	redhat-rpm-config \
	rpm-build \
    tcl-devel \
	tcsh \
	time \
	tk-devel \
    wget \
    which \
    zlib \
    zlib-devel && mkdir -p /home/parflow
    
    #-----------------------------------------------------------------------------
    # Set environment vars
    #-----------------------------------------------------------------------------
    PARFLOW_DIR=/usr/local
    PATH=$PATH:/usr/lib64/openmpi/bin:$PARFLOW_DIR/bin
    LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib64/openmpi/lib
    OMPI_DIR=/opt/ompi
    SINGULARITY_OMPI_DIR=$OMPI_DIR
    SINGULARITYENV_APPEND_PATH=$OMPI_DIR/bin
    SINGULAIRTYENV_APPEND_LD_LIBRARY_PATH=$OMPI_DIR/lib
	
    #-----------------------------------------------------------------------------
    # Install Mellanox Driver for Infiniband Support within container
    #-----------------------------------------------------------------------------
	yum install -y http://vault.centos.org/7.4.1708/updates/x86_64/Packages/kernel-devel-3.10.0-693.17.1.el7.x86_64.rpm
	OFED_URL="http://www.mellanox.com/downloads/ofed/MLNX_OFED-4.6-1.0.1.1/MLNX_OFED_LINUX-4.6-1.0.1.1-rhel7.4-x86_64.tgz" 
	
	mkdir -p /tmp/mlx

	cd /tmp/mlx && wget -O MLNX_OFED_LINUX-4.6-1.0.1.1-rhel7.4-x86_64.tgz $OFED_URL && \
	tar -xf MLNX_OFED_LINUX-4.6-1.0.1.1-rhel7.4-x86_64.tgz && \
	cd MLNX_OFED_LINUX-4.6-1.0.1.1-rhel7.4-x86_64/ && \
	./mlnxofedinstall --without-fw-update --force --skip-distro-check --distro rhel7.4 --add-kernel-support --kernel 3.10.0-693.17.1.el7.x86_64 --kernel-sources /usr/src/kernels/3.10.0-693.17.1.el7.x86_64
	


    #-----------------------------------------------------------------------------
    # Build libraries
    #-----------------------------------------------------------------------------
    
    #
    # OMPI
    # 
    echo "Installing Open MPI"
    export OMPI_DIR=/opt/ompi
    export OMPI_VERSION=4.0.1
    export
	OMPI_URL="https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-$OMPI_VERSION.tar.gz"
    mkdir -p /tmp/ompi
    mkdir -p /opt
    # Download
    cd /tmp/ompi && wget -O openmpi-$OMPI_VERSION.tar.gz $OMPI_URL && tar -xf openmpi-$OMPI_VERSION.tar.gz
    # Compile and install
    cd /tmp/ompi/openmpi-$OMPI_VERSION && ./configure --prefix=$OMPI_DIR && make install -j2
    # Set env variables so we can
	# compile our application
    export PATH=$OMPI_DIR/bin:$PATH
    export LD_LIBRARY_PATH=$OMPI_DIR/lib:$LD_LIBRARY_PATH
    export MANPATH=$OMPI_DIR/share/man:$MANPATH

    #
    # SILO 
    #
    curl "https://wci.llnl.gov/content/assets/docs/simulation/computer-codes/silo/silo-4.10.2/silo-4.10.2.tar.gz" -o "silo-4.10.2.tar.gz" && \
    tar -xf silo-4.10.2.tar.gz && \
    cd silo-4.10.2 && \
    ./configure  --prefix=$PARFLOW_DIR --disable-silex --disable-hzip --disable-fpzip FC=/usr/bin/gfortran F77=/usr/bin/gfortran && \
    make install -j2 && \
    cd .. && \
    rm -fr silo-4.10.2 silo-4.10.2.tar.gz
    
    #
    # Hypre
    #
    cd /home/parflow
    source /etc/profile.d/modules.sh && \
    module load mpi/openmpi-x86_64 && \
    git clone -b master --single-branch https://github.com/LLNL/hypre.git hypre && \
    cd hypre/src && \
    ./configure --prefix=$PARFLOW_DIR && \
    make install -j2 && \
    cd ../.. && \
    rm -fr hypre
    
    #-----------------------------------------------------------------------------
    # Parflow configure and build
    #-----------------------------------------------------------------------------
    
    PARFLOW_MPIEXEC_EXTRA_FLAGS="--mca mpi_yield_when_idle 1 --oversubscribe --allow-run-as-root"
    
    cd /home/parflow
    
    git clone -b master --single-branch https://github.com/parflow/parflow.git parflow && \
    mkdir -p build && \
    cd build && \
    cmake3 ../parflow \
    -DPARFLOW_AMPS_LAYER=mpi1 \
    -DPARFLOW_AMPS_SEQUENTIAL_IO=FALSE \
    -DHYPRE_ROOT=$PARFLOW_DIR \
    -DSILO_ROOT=$PARFLOW_DIR \
	-DPARFLOW_ENABLE_HDF5=TRUE \
    -DPARFLOW_ENABLE_TIMING=TRUE \
    -DPARFLOW_HAVE_CLM=ON \
    -DCMAKE_INSTALL_PREFIX=$PARFLOW_DIR && \
    make install -j2 && \
    cd .. && \
    rm -fr parflow build

%environment
    export PARFLOW_DIR=/usr/local
    export PATH=$PATH:/usr/lib64/openmpi/bin:$PARFLOW_DIR/bin
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib64/openmpi/lib
    export PARFLOW_MPIEXEC_EXTRA_FLAGS="--mca mpi_yield_when_idle 1 --oversubscribe --allow-run-as-root"
    export OMPI_DIR=/opt/ompi
    export SINGULARITY_OMPI_DIR=$OMPI_DIR
    export SINGULARITYENV_APPEND_PATH=$OMPI_DIR/bin
    export SINGULAIRTYENV_APPEND_LD_LIBRARY_PATH=$OMPI_DIR/lib

%runscript
    exec tclsh "$@"

%startscript
    exec tclsh "$@"
