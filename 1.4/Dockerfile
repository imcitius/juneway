FROM centos

RUN yum -y groupinstall "Development Tools" && yum -y install wget git pcre-devel zlib-devel
RUN wget https://github.com/openresty/luajit2/archive/v2.1-20200102.tar.gz && tar -xzvf v2.1-20200102.tar.gz
RUN cd luajit2-2.1-20200102 && make && make install && cd ..
RUN wget https://github.com/vision5/ngx_devel_kit/archive/v0.3.1.tar.gz && tar -xzvf v0.3.1.tar.gz
RUN wget https://github.com/openresty/lua-nginx-module/archive/v0.10.17.tar.gz && tar -xzvf v0.10.17.tar.gz

RUN wget http://nginx.org/download/nginx-1.18.0.tar.gz && tar -xzvf nginx-1.18.0.tar.gz && cd nginx-1.18.0 && \
    export LUAJIT_LIB=/usr/local/lib/ LUAJIT_INC=/usr/local/include/luajit-2.1 && \
    ./configure --with-ld-opt="-Wl,-rpath,/usr/local/lib/" --add-module=/ngx_devel_kit-0.3.1 --add-module=/lua-nginx-module-0.10.17 && \
    make && make install

FROM centos
RUN mkdir /usr/local/nginx
COPY --from=0 /usr/local/nginx /usr/local/nginx
COPY --from=0 /usr/local/lib/libluajit-5.1.a /usr/local/lib/
COPY --from=0 /usr/local/lib/libluajit-5.1.so.2.1.0 /usr/local/lib/
RUN cd /usr/local/lib/ && ln -sf libluajit-5.1.so.2.1.0 libluajit-5.1.so && ln -sf libluajit-5.1.so.2.1.0 libluajit-5.1.so.2
