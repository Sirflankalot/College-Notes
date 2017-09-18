cmake_minimum_required(VERSION 3.1)
project(college-notes NONE)

include(CMakeParseArguments)

function(add_notes) 
	cmake_parse_arguments(NOTES "" "NAME" "FILES" ${ARGN})

	foreach (file ${NOTES_FILES})
		get_filename_component(OUTPUT_NAME ${CMAKE_CURRENT_BINARY_DIR}/${file} NAME_WE)

		add_custom_command(OUTPUT ${OUTPUT_NAME}.html
			               COMMAND pandoc ARGS ${CMAKE_CURRENT_SOURCE_DIR}/${file} -f markdown+tex_math_dollars -t html -s --mathjax -o ${OUTPUT_NAME}.html
			               DEPENDS ${file})
		set(NOTES_DEPEND ${NOTES_DEPEND} "${OUTPUT_NAME}.html")
	endforeach()

	add_custom_target(${NOTES_NAME} ALL DEPENDS ${NOTES_DEPEND})
endfunction(add_notes)

add_subdirectory(ANTHC101)
# add_subdirectory(CS127)
# add_subdirectory(MATH150)
# add_subdirectory(STAT213)
