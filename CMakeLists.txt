cmake_minimum_required(VERSION 3.1)
project(college-notes NONE)

include(CMakeParseArguments)

function(add_notes) 
	cmake_parse_arguments(NOTES "DISCRETE_STRUCTURES" "NAME" "FILES" ${ARGN})

	foreach (file ${NOTES_FILES})
		get_filename_component(OUTPUT_NAME ${CMAKE_CURRENT_BINARY_DIR}/${file} NAME_WE)

		if (NOTES_DISCRETE_STRUCTURES)
			add_custom_command(OUTPUT ${OUTPUT_NAME}.md.preprocessed
							   COMMAND sed ARGS -E -n "s/\\([[:digit:]]+\\):\\([[:digit:]]+\\)/[\\\\1:\\\\2]\\(note\\\\1.pdf#page=\\\\2\\)/g\\;w ${OUTPUT_NAME}.md.preprocessed" ${CMAKE_CURRENT_SOURCE_DIR}/${file}
							   DEPENDS ${file}
			    )
		else()
			add_custom_command(OUTPUT ${OUTPUT_NAME}.md.preprocessed
							   COMMAND ${CMAKE_COMMAND} ARGS -E copy ${CMAKE_CURRENT_SOURCE_DIR}/${file} ${CMAKE_CURRENT_BINARY_DIR}/${OUTPUT_NAME}.md.preprocessed
							   DEPENDS ${file}
				)
		endif()

		add_custom_command(OUTPUT ${OUTPUT_NAME}.html
						   COMMAND pandoc ARGS -H ${CMAKE_SOURCE_DIR}/mathjax.html  ${OUTPUT_NAME}.md.preprocessed -f markdown_github+tex_math_dollars -t html -s --mathjax --css ${CMAKE_SOURCE_DIR}/pandoc.css -o ${OUTPUT_NAME}.html
						   DEPENDS ${OUTPUT_NAME}.md.preprocessed ${CMAKE_SOURCE_DIR}/pandoc.css ${CMAKE_SOURCE_DIR}/mathjax.html)

		set(NOTES_DEPEND ${NOTES_DEPEND} "${OUTPUT_NAME}.html")
	endforeach()

	add_custom_target(${NOTES_NAME} ALL DEPENDS ${NOTES_DEPEND})
endfunction(add_notes)

add_subdirectory(ANTHC101)
add_subdirectory(CS127)
add_subdirectory(CS150)
add_subdirectory(MATH150)
add_subdirectory(STAT213)
