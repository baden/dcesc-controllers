.SILENT:
# .SECONDARY:
.PHONY: kicad remap rewidth

# TODO: Make multiple boards support, like a
# BOARDS = FXA500-01 FXA500-02

# Now just one board is supported
BOARD = FXA500-02

BOARD_DIR = ./boards/${BOARD}
DIST_DIR = ./dist/${BOARD}
TEMP_DIR = ./.tmp/${BOARD}

DPI_PNG = $${1:-1000}

GERBER_LIST := F_Cu.gtl B_Cu.gbl In1_Cu In2_Cu

# Destination

PNG_LIST := F_Cu B_Cu In1_Cu In2_Cu

GERBER_TEMP_FILES := $(addprefix ${TEMP_DIR}/${BOARD}-, ${GERBER_LIST})
PNG_FILES := $(patsubst %, ${DIST_DIR}/files/${BOARD}-%.png, ${PNG_LIST}) \
			 ${DIST_DIR}/files/front_natural.png \
			 ${DIST_DIR}/files/back_natural.png

GERBER_ARCHIVE := ${DIST_DIR}/${BOARD}-gerbers.zip
ASSEMBLE_ARCHIVE := ${DIST_DIR}/${BOARD}-assemble.zip

PYTHON := /Applications/KiCad/KiCad.app/Contents/Frameworks/Python.framework/Versions/3.9/bin/python3.9
INKSCAPE := inkscape
#INKSCAPE := snap run inkscape

all: ${GERBER_ARCHIVE} ${ASSEMBLE_ARCHIVE} ${PNG_FILES} ${DIST_DIR}/${BOARD}.txt

clean:
	rm -rf ./.tmp

dist-clean: clean
	rm -rf ./dist

#
# use this like:
# 'make print-PATH print-CFLAGS make print-ALL_OBJS'
# to see the value of make variable PATH and CFLAGS, ALL_OBJS, etc.
#
print-%:
	@echo $* is $($*)


kicad:
	cd ${BOARD_DIR} && kicad ./${BOARD}.pro


# rewidth: board/${BOARD}.kicad_pcb
# 	cp board/${BOARD}.kicad_pcb board/${BOARD}.kicad_pcb_back_$(shell date '+%y%m%d_%H%M%S')
# 	python scripts/change_track_widths.py $(abspath board/${BOARD}.kicad_pcb)



board/.lib/device.lib:
board/.lib/device.lib:

#
# Main stages
#

${DIST_DIR}/${BOARD}.txt: ${BOARD_DIR}/docs/${BOARD}.txt
	@mkdir -p $(dir $@)
	cp $< $@

${GERBER_ARCHIVE}: \
	${TEMP_DIR}/${BOARD}-F_Cu.gtl \
    ${TEMP_DIR}/${BOARD}-B_Cu.gbl \
    ${TEMP_DIR}/${BOARD}-In1_Cu.g2 \
    ${TEMP_DIR}/${BOARD}-In2_Cu.g3 \
    ${TEMP_DIR}/${BOARD}-F_Mask.gts \
    ${TEMP_DIR}/${BOARD}-B_Mask.gbs \
    ${TEMP_DIR}/${BOARD}-Edge_Cuts.gm1 \
    ${TEMP_DIR}/${BOARD}-F_Paste.gtp \
    ${TEMP_DIR}/${BOARD}-B_Paste.gbp \
    ${TEMP_DIR}/${BOARD}-F_SilkS.gto \
    ${TEMP_DIR}/${BOARD}-B_SilkS.gbo \
    ${TEMP_DIR}/${BOARD}.drl \
    ${TEMP_DIR}/${BOARD}-drl_map.pdf

	@echo "Create gerber archive"
	@mkdir -p $(dir $@)
	@rm -f $@
	zip -jq $@ $?

${ASSEMBLE_ARCHIVE}: \
	${TEMP_DIR}/${BOARD}-F_Fab.pdf ${TEMP_DIR}/${BOARD}-F_Fab.png \
    ${TEMP_DIR}/${BOARD}-B_Fab.pdf ${TEMP_DIR}/${BOARD}-B_Fab.png

	@echo "Create assemble archive"
	@mkdir -p $(dir $@)
	@rm -f $@
	zip -jq $@ $?

# TODO: WTF with this rules?

${DIST_DIR}/files/${BOARD}-F_Cu.png: ${TEMP_DIR}/${BOARD}-F_Cu.gtl
${DIST_DIR}/files/${BOARD}-B_Cu.png: ${TEMP_DIR}/${BOARD}-B_Cu.gbl
${DIST_DIR}/files/${BOARD}-In1_Cu.png: ${TEMP_DIR}/${BOARD}-In1_Cu.g2
${DIST_DIR}/files/${BOARD}-In2_Cu.png: ${TEMP_DIR}/${BOARD}-In2_Cu.g3

${DIST_DIR}/files/%.png:
	@echo "Convert $< to $@"
	mkdir -p $(dir $@)
	gerbv "$<" -o "$@" --dpi=${DPI_PNG}x${DPI_PNG} -a --export=png --background="#FFFFFF" --foreground="#000000FF"

# Тут более сложная зависимость, от нескольких файлов
${DIST_DIR}/files/front_natural.png: scripts/${BOARD}/front_natural.gvp \
	${TEMP_DIR}/${BOARD}-Edge_Cuts.gm1 \
	${TEMP_DIR}/${BOARD}-F_Cu.gtl \
	${TEMP_DIR}/${BOARD}-F_Mask.gts \
	${TEMP_DIR}/${BOARD}-F_Paste.gtp \
	${TEMP_DIR}/${BOARD}-F_SilkS.gto \
	${TEMP_DIR}/${BOARD}.drl

	@echo "Convert $< to $@"
	gerbv -p "$<" -o "$@" --dpi=${DPI_PNG}x${DPI_PNG} -a --export=png

${DIST_DIR}/files/back_natural.png: scripts/${BOARD}/back_natural.gvp \
	${TEMP_DIR}/${BOARD}-Edge_Cuts.gm1 \
	${TEMP_DIR}/${BOARD}-B_Cu.gbl \
	${TEMP_DIR}/${BOARD}-B_Mask.gbs \
	${TEMP_DIR}/${BOARD}-B_Paste.gbp \
	${TEMP_DIR}/${BOARD}-B_SilkS.gbo \
	${TEMP_DIR}/${BOARD}.drl

	@echo "Convert $< to $@"
	gerbv -p "$<" -o "$@" --dpi=${DPI_PNG}x${DPI_PNG} -a --export=png
	convert "$@" -flip -rotate 180 "$@"

	# convert ./PNGs/${BOARD}-B_Fab.gbr.png -flip -rotate 180 ./PNGs/${BOARD}-B_Fab.gbr.png

#
# Temp stages
#

${TEMP_DIR}/${BOARD}-F_Fab.pdf: ${TEMP_DIR}/${BOARD}-F_Fab.svg
	@echo "Make $@."
	${INKSCAPE} -z $< -D --export-pdf=$@ > /dev/null 2> /dev/null

${TEMP_DIR}/${BOARD}-F_Fab.png: ${TEMP_DIR}/${BOARD}-F_Fab.svg
	@echo "Make $@."
	${INKSCAPE} -z $< -w 1024 -D --export-png=$@ --export-background-opacity=1.0 > /dev/null 2> /dev/null

${TEMP_DIR}/${BOARD}-B_Fab.pdf: ${TEMP_DIR}/${BOARD}-B_Fab.svg
	@echo "Make $@."
	${INKSCAPE} -z $< -D --export-pdf=$@ > /dev/null 2> /dev/null

${TEMP_DIR}/${BOARD}-B_Fab.png: ${TEMP_DIR}/${BOARD}-B_Fab.svg
	@echo "Make $@."
	${INKSCAPE} -z $< -w 1024 -D --export-png=$@ --export-background-opacity=1.0 > /dev/null 2> /dev/null

${TEMP_DIR}/${BOARD}-F_Cu.gtl \
${TEMP_DIR}/${BOARD}-B_Cu.gbl \
${TEMP_DIR}/${BOARD}-In1_Cu.g2 \
${TEMP_DIR}/${BOARD}-In2_Cu.g3 \
${TEMP_DIR}/${BOARD}-B_Fab.gbr \
${TEMP_DIR}/${BOARD}-B_Fab.svg \
${TEMP_DIR}/${BOARD}-B_Mask.gbs \
${TEMP_DIR}/${BOARD}-Eco2_User.gbr \
${TEMP_DIR}/${BOARD}-Edge_Cuts.gm1 \
${TEMP_DIR}/${BOARD}-F_Fab.gbr \
${TEMP_DIR}/${BOARD}-F_Fab.svg \
${TEMP_DIR}/${BOARD}-F_Mask.gts \
${TEMP_DIR}/${BOARD}-F_Paste.gtp \
${TEMP_DIR}/${BOARD}-B_Paste.gbp \
${TEMP_DIR}/${BOARD}-F_SilkS.gto \
${TEMP_DIR}/${BOARD}-B_SilkS.gbo \
${TEMP_DIR}/${BOARD}.drl \
${TEMP_DIR}/${BOARD}-drl_map.pdf: tempstage

.INTERMEDIATE: tempstage

tempstage: ${BOARD_DIR}/${BOARD}.kicad_pcb
	@echo "!!!!!!!!!!!!!!!!!!!! This must be run once! ($? -> $@)"
	@mkdir -p ${TEMP_DIR}
	${PYTHON} ./scripts/kicad-fab.py ${BOARD_DIR}/${BOARD}.kicad_pcb ${TEMP_DIR}
