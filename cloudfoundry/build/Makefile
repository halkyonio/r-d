all :: paketo/paketo-builder

paketo/paketo-builder: builder.toml paketo/adopt-openjdk/buildpack.toml paketo/spring-boot/buildpack.toml
	pack create-builder cmoulliard/paketo-spring-boot-builder --builder-config builder.toml
	mkdir -p paketo
	touch paketo/paketo-spring-boot-builder

define BUILDPACK
paketo/$(1)/buildpack.toml:
	mkdir -p paketo/$(1)
	curl -Ls https://github.com/paketo-buildpacks/$(1)/archive/$(2).tar.gz | tar -x --strip-components=1 -C paketo/$(1) -z -f -
	sed -i'' -e 's/{{\s*\.version}}/$(2)/g' paketo/$(1)/buildpack.toml
	#./paketo/$(1)/scripts/build.sh
endef

$(eval $(call BUILDPACK,spring-boot,v1.5.1))
$(eval $(call BUILDPACK,adopt-openjdk,v2.2.1))
#$(eval $(call BUILDPACK,bellsoft-liberica,v2.3.1))