.SUFFIXES: .java .m
.PHONY: default clean translate link

include ../../../resources/make/common.mk

MAIN_SRC_DIR = src
ELEMENTAL_GEN_DIR = GDJson/Classes/generated/json
MAIN_SOURCES = \
  $(MAIN_SRC_DIR)/elemental/json/Json.java \
  $(MAIN_SRC_DIR)/elemental/json/JsonArray.java \
  $(MAIN_SRC_DIR)/elemental/json/JsonBoolean.java \
  $(MAIN_SRC_DIR)/elemental/json/JsonException.java \
  $(MAIN_SRC_DIR)/elemental/json/JsonFactory.java \
  $(MAIN_SRC_DIR)/elemental/json/JsonNull.java \
  $(MAIN_SRC_DIR)/elemental/json/JsonNumber.java \
  $(MAIN_SRC_DIR)/elemental/json/JsonObject.java \
  $(MAIN_SRC_DIR)/elemental/json/JsonString.java \
  $(MAIN_SRC_DIR)/elemental/json/JsonType.java \
  $(MAIN_SRC_DIR)/elemental/json/JsonValue.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JreJsonArray.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JreJsonBoolean.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JreJsonFactory.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JreJsonNull.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JreJsonNumber.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JreJsonObject.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JreJsonString.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JreJsonValue.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JsonArrayContext.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JsonContext.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JsonObjectContext.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JsonTokenizer.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JsonUtil.java \
  $(MAIN_SRC_DIR)/elemental/json/impl/JsonVisitor.java
MAIN_TEMP_SOURCES = $(subst $(MAIN_SRC_DIR), $(ELEMENTAL_GEN_DIR), $(MAIN_SOURCES))
MAIN_GEN_SOURCES = $(MAIN_TEMP_SOURCES:.java=.m)
MAIN_TEMP_SOURCES2 = $(subst $(MAIN_SRC_DIR), $(BUILD_DIR), $(MAIN_SOURCES))
MAIN_OBJECTS = $(MAIN_TEMP_SOURCES2:.java=.o)
SUPPORT_LIB = $(BUILD_DIR)/libElemental.a

default: clean link

translate: translate_main

pre_translate_main: $(BUILD_DIR) $(ELEMENTAL_GEN_DIR)
	@rm -f $(MAIN_SOURCE_LIST)
	@touch $(MAIN_SOURCE_LIST)
        
$(ELEMENTAL_GEN_DIR)/%.m $(ELEMENTAL_GEN_DIR)/%.h: $(MAIN_SRC_DIR)/%.java
	@echo $? >> $(MAIN_SOURCE_LIST)

translate_main: pre_translate_main $(MAIN_GEN_SOURCES)
	@if [ `cat $(MAIN_SOURCE_LIST) | wc -l` -ge 1 ] ; then \
	  $(J2OBJC) -sourcepath '$(MAIN_SRC_DIR):idl/generated/src' -d $(ELEMENTAL_GEN_DIR) \
	    -classpath '$(M2_REPO)/com/google/gwt/gwt-user/2.5.1/gwt-user-2.5.1.jar' \
	    `cat $(MAIN_SOURCE_LIST)` ; \
	fi
	@cd $(ELEMENTAL_GEN_DIR);tar -c . | tar -x -C ../include --include=*.h

$(BUILD_DIR)/%.o: $(ELEMENTAL_GEN_DIR)/%.m $(MAIN_SRC_DIR)/%.java
	@mkdir -p `dirname $@`
	@$(J2OBJCC) -c $< -o $@ -g -I$(ELEMENTAL_GEN_DIR)

$(SUPPORT_LIB): $(MAIN_OBJECTS)
	libtool -static -o $(SUPPORT_LIB) $(MAIN_OBJECTS)

link: translate $(SUPPORT_LIB)

$(ELEMENTAL_GEN_DIR):
	@mkdir -p $(ELEMENTAL_GEN_DIR)
	@mkdir -p $(ELEMENTAL_GEN_DIR)/../include
$(BUILD_DIR):
	@mkdir -p $(BUILD_DIR)

clean:
	@rm -rf $(ELEMENTAL_GEN_DIR) $(BUILD_DIR)
