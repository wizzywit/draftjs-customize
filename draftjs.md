# editor component
```jsx
import React, { useRef, useState } from 'react';
import Editor from 'draft-js-plugins-editor';
import createMarkdownShortcutsPlugin from 'draft-js-markdown-shortcuts-plugin';
import { Box, Typography } from '@mui/material';
import {
  DraftHandleValue,
  EditorState,
  getDefaultKeyBinding,
  KeyBindingUtil,
  RichUtils,
} from 'draft-js';
import {
  changeDepth,
  getCustomStyleMap,
  handleNewLine,
  blockRenderMap,
} from 'draftjs-utils';
import PluginEditor from 'draft-js-plugins-editor';
import Bold from '../static-assets/Bold.png';
import Underline from '../static-assets/Underline.png';
import Italic from '../static-assets/Italic.png';
import Ordered from '../static-assets/Ordered.png';
import Unordered from '../static-assets/Unordered.png';
import * as StyledComponent from './Incidents.styles';
import 'draft-js/dist/Draft.css';
import { ControlValues } from './Incidents.types';
import Control from './Incidents.editorControl.component';

const plugins = [createMarkdownShortcutsPlugin()];

interface OwnProps {
  setEditorState: React.Dispatch<React.SetStateAction<EditorState>>;
  editorState: EditorState;
}

const CustomEditor = (props: OwnProps) => {
  const [plugins] = useState(() => {
    const markdown = createMarkdownShortcutsPlugin();
    return [markdown];
  });
  const { editorState, setEditorState } = props;
  const wrapperRef = useRef<HTMLDivElement>(null);
  const editorRef = useRef<PluginEditor>(null);

  const controlProps = {
    editorState,
    onChange: setEditorState,
  };

  const focusEditor = () => {
    setTimeout(() => {
      editorRef?.current?.focus();
    });
  };

  const keyBindingFn = (event: React.KeyboardEvent) => {
    if (event.key === 'Tab') {
      const newEditorState = changeDepth(
        editorState,
        event.shiftKey ? -1 : 1,
        4,
      );
      if (editorState && editorState !== editorState) {
        setEditorState(newEditorState);
        event.preventDefault();
      }
    }
    if (
      KeyBindingUtil.hasCommandModifier(event) &&
      event.shiftKey &&
      event.key === 'x'
    ) {
      return 'strikethrough';
    }

    if (
      KeyBindingUtil.hasCommandModifier(event) &&
      event.shiftKey &&
      event.key === '7'
    ) {
      return 'ordered-list';
    }

    if (
      KeyBindingUtil.hasCommandModifier(event) &&
      event.shiftKey &&
      event.key === '8'
    ) {
      return 'unordered-list';
    }

    if (
      KeyBindingUtil.hasCommandModifier(event) &&
      event.shiftKey &&
      event.key === '9'
    ) {
      return 'blockquote';
    }

    return getDefaultKeyBinding(event);
  };

  const handleReturn = (
    event: React.KeyboardEvent,
    state: EditorState,
  ): DraftHandleValue => {
    const newEditorState = handleNewLine(state, event);
    if (newEditorState) {
      setEditorState(newEditorState);
      return 'handled';
    }
    return 'not-handled';
  };

  const handleKeyCommand = (command: string) => {
    let newEditorState: null | EditorState = RichUtils.handleKeyCommand(
      editorState,
      command,
    );

    if (!newEditorState && command === 'ordered-list') {
      newEditorState = RichUtils.toggleBlockType(
        editorState,
        'ordered-list-item',
      );
    }

    if (!newEditorState && command === 'unordered-list') {
      newEditorState = RichUtils.toggleBlockType(
        editorState,
        'unordered-list-item',
      );
    }

    if (newEditorState) {
      setEditorState(newEditorState);
      return 'handled';
    }

    return 'not-handled';
  };

  const controls: {
    value: ControlValues;
    icon: string;
    style: 'inline' | 'list';
  }[] = [
    {
      value: ControlValues.Bold,
      icon: Bold,
      style: 'inline',
    },
    {
      value: ControlValues.Underline,
      icon: Underline,
      style: 'inline',
    },
    {
      value: ControlValues.Italic,
      icon: Italic,
      style: 'inline',
    },
    {
      value: ControlValues.Ordered,
      icon: Ordered,
      style: 'list',
    },
    {
      value: ControlValues.Unordered,
      icon: Unordered,
      style: 'list',
    },
  ];

  return (
    <StyledComponent.EditorContainer width="100%">
      <StyledComponent.EditorWrapper
        id="rdw-wrapper-wrapperId"
        aria-label="rdw-wrapper"
      >
        <StyledComponent.EditorToolBar aria-label="rdw-toolbar">
          <Box marginRight="auto">
            <Typography
              variant="subtitle1"
              textAlign="center"
              marginRight="auto"
            >
              Write a comment
            </Typography>
          </Box>
          <StyledComponent.ControlWrapper aria-label="rdw-inline-control">
            {controls.map(({ value, icon, style }) => (
              <Control
                key={value}
                {...controlProps}
                value={value}
                icon={icon}
                style={style}
              />
            ))}
          </StyledComponent.ControlWrapper>
        </StyledComponent.EditorToolBar>
        <StyledComponent.EditorMain ref={wrapperRef} onClick={focusEditor}>
          <Editor
            ref={editorRef}
            keyBindingFn={keyBindingFn}
            editorState={editorState}
            onChange={setEditorState}
            customStyleMap={getCustomStyleMap()}
            handleReturn={handleReturn}
            ariaLabel={'rdw-editor'}
            blockRenderMap={blockRenderMap}
            handleKeyCommand={handleKeyCommand}
            plugins={plugins}
          />
        </StyledComponent.EditorMain>
      </StyledComponent.EditorWrapper>
      <StyledComponent.EditorFooter>
        <Typography variant="subtitle2">Supports Markdown.</Typography>
      </StyledComponent.EditorFooter>
    </StyledComponent.EditorContainer>
  );
};

export default CustomEditor;

```

# Editor Styles
```jsx
export const EditorContainer = styled(Box)({
  border: '1px solid #C9C9C9',
  borderRadius: 5,
  padding: 7,
  backgroundColor: 'white',
});

export const EditorFooter = styled(Box)({
  border: 'none',
  borderTop: '1px solid #C9C9C9',
  padding: 7,
});

export const EditorWrapper = styled(Box)({
  boxSizing: 'content-box',
});

export const EditorToolBar = styled(Box)({
  border: 'none',
  borderBottom: '1px solid #C9C9C9',
  padding: 3,
  paddingTop: 11,
  justifyContent: 'flex-end',
  visibility: 'visible',
  borderRadius: 2,
  display: 'flex',
  background: 'white',
  flexWrap: 'wrap',
  fontSize: 15,
  marginBottom: 5,
  userSelect: 'none',
  paddingBottom: 3,
  paddingRight: 5,
  paddingLeft: 5,
});

export const ControlWrapper = styled(Box)({
  display: 'flex',
  alignItems: 'center',
  marginBottom: 6,
  flexWrap: 'wrap',
});

export const OptionWrapper = styled(Box)({
  border: 'none',
  padding: 5,
  minWidth: 25,
  height: 20,
  borderRadius: 2,
  margin: '0 4px',
  display: 'flex',
  justifyContent: 'center',
  alignItems: 'center',
  cursor: 'pointer',
  background: 'white',
  textTransform: 'capitalize',
  img: {
    height: 12,
  },
  '&:active': {
    boxShadow: '1px 1px 0px #bfbdbd inset',
  },
  '&:hover': {
    boxShadow: '1px 1px 0px #bfbdbd',
  },
});

export const ActiveBox = styled(Box)({
  display: 'flex',
  justifyContent: 'center',
  alignItems: 'center',
  boxShadow: '1px 1px 0px #bfbdbd inset',
  width: '100%',
  height: '100%',
});

export const EditorMain = styled(Box)({
  height: '100%',
  overflow: 'auto',
  boxSizing: 'border-box',
  paddingTop: 5,
  paddingBottom: 5,
  paddingRight: 10,
  paddingLeft: 10,
  minHeight: 100,
});
```

# editor control
```jsx
import React, { useMemo } from 'react';
import { EditorState, RichUtils } from 'draft-js';
import { ControlValues } from './Incidents.types';
import Option from './Incidents.editorOption.component';

interface OwnProps {
  onChange: React.Dispatch<React.SetStateAction<EditorState>>;
  editorState: EditorState;
  value: ControlValues;
  icon: string;
  style: 'inline' | 'list';
}
const Control = ({ onChange, editorState, value, icon, style }: OwnProps) => {
  const currentInlineStyle = editorState.getCurrentInlineStyle();
  const currentBlockStyle = RichUtils.getCurrentBlockType(editorState);

  const active = useMemo(() => {
    switch (style) {
      case 'inline':
        return currentInlineStyle.has(value.toUpperCase());
      case 'list':
        return currentBlockStyle === value;
    }
    return true;
  }, [currentInlineStyle, currentBlockStyle, style, value]);

  return (
    <Option value={value} onChange={onChange} style={style} active={active}>
      <img alt="" src={icon} />
    </Option>
  );
};

export default Control;
```

# editor option
```jsx
import React from 'react';
import { EditorState, RichUtils } from 'draft-js';
import * as StyledComponent from './Incidents.styles';
import { ControlValues } from './Incidents.types';

interface OwnProps {
  onChange: React.Dispatch<React.SetStateAction<EditorState>>;
  children?: React.ReactNode;
  active?: boolean;
  value: ControlValues;
  style: 'inline' | 'list';
}

const Option = (props: OwnProps) => {
  const { children, active, onChange, value, style } = props;

  const toggleInlineStyle = (e: React.SyntheticEvent) => {
    e.preventDefault();
    switch (style) {
      case 'inline':
        onChange((editorState) =>
          RichUtils.toggleInlineStyle(editorState, value.toUpperCase()),
        );
        break;
      case 'list':
        onChange((editorState) =>
          RichUtils.toggleBlockType(editorState, value),
        );
        break;
    }
  };

  return (
    <StyledComponent.OptionWrapper
      aria-selected={active}
      onMouseDown={toggleInlineStyle}
    >
      {active ? (
        <StyledComponent.ActiveBox>{children}</StyledComponent.ActiveBox>
      ) : (
        children
      )}
    </StyledComponent.OptionWrapper>
  );
};

export default Option;
```

# controlValue type
```jsx
export enum ControlValues {
  Bold = 'bold',
  Underline = 'underline',
  Italic = 'italic',
  Ordered = 'ordered-list-item',
  Unordered = 'unordered-list-item',
}
```

# type declaration
```jsx
declare module 'draft-js-markdown-shortcuts-plugin' {
  import { EditorPlugin } from 'draft-js-plugins-editor';
  export default function createMarkdownShortcutsPlugin(): EditorPlugin;
}

declare module 'draftjs-utils' {
  import { DraftBlockRenderConfig, DraftStyleMap, EditorState } from 'draft-js';
  import React from 'react';

  export function changeDepth(
    editorState: EditorState,
    adjustment: number,
    maxDepth: number,
  ): EditorState;

  export function handleNewLine(
    editorState: EditorState,
    event: React.KeyboardEvent,
  ): EditorState;

  export function getCustomStyleMap(): DraftStyleMap | undefined;
  export const blockRenderMap: Immutable.Map<string, DraftBlockRenderConfig>;
}
```
